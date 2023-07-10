---
title: UGUI优化-动态图集
author: 灵
comment: 'on'
tags:
  - UGUI
category:
  - Unity
  - UGUI
date: 2023-07-10 18:27:06
---
# 主意
Graphics.CopyTexture 貌似只能图片格式是RGBA32

不能这个格式就读写像素


```csharp

    public RawImage raw;
    class Content
    {
        public Rect rect;

        private Page root;
        List<Page> work = new List<Page>();
        List<Page> empty = new List<Page>();
        public Content(Rect rect)
        {
            this.rect = rect;
            work = new List<Page>() {
                new Page()
                {
                    rect=rect,
                }
            };
            root = work[0];
        }
        private List<Page> success = new List<Page>();
        private void Split(Rect rect, Vector2 size, Vector2 smallest)
        {
            float x = rect.width - size.x;
            float y = rect.height - size.y;
            var page1 = new Page();
            var page2 = new Page();

            if (x < smallest.x && y > smallest.y)
            {
                page1.rect = new Rect(rect.x + size.x, rect.y, x, size.y);
                page2.rect = new Rect(rect.x, rect.y + size.y, rect.width, y);
            }
            else
            {
                page1.rect = new Rect(rect.x + size.x, rect.y, x, rect.height);
                page2.rect = new Rect(rect.x, rect.y + size.y, size.x, y);
            }
            empty.Add(page1);
            empty.Add(page2);
        }
        public Page RealAdd(Page page, Vector2 size, Vector2 smallest)
        {
            work.Remove(page);
            Page page0 = new Page()
            {
                rect = new Rect(page.rect.position, size)
            };
            work.Add(page0);
            Split(page.rect, size, smallest);
            return page0;
        }
        public static bool Try(Page page, Vector2 size)
        {
            if (page.rect.size.x < size.x || page.rect.height < size.y)
                return false;
            return true;
        }

        public Page Add(Vector2 size, Vector2 smallest)
        {
            if (rect.size.x < size.x || rect.height < size.y)
                return null;
            if (root != null)
            {
                if (Try(root, size))
                {
                    Page p = RealAdd(root, size, smallest);
                    root = null;
                    return p;
                }
                return null;
            }
            else
            {
                for (int i = 0; i < empty.Count; i++)
                {
                    Page page = empty[i];
                    if (Try(page, size))
                    {
                        success.Add(page);
                    }
                }
                if (success.Count > 0)
                {
                    success.Sort((a, b) => { return a.rect.size.x * a.rect.size.y < b.rect.size.x * b.rect.size.y ? -1 : 1; });
                    Page p = RealAdd(success[0], size, smallest);
                    success.Clear();
                    return p;
                }
                return null;
            }

        }
    }
    class Page
    {
        public Rect rect;
    }
    public void GG(Image[] images)
    {
        var dic = images.ToDictionary(x => x.sprite);
        var sprites = images.ToList().ConvertAll(x => x.sprite).ToList();
        int size = 2048;

        Content c = new Content(new Rect(Vector2.zero, Vector2.one * size));

        sprites.Sort((a, b) => { return a.rect.width * a.rect.height < b.rect.width * b.rect.height ? 1 : -1; });
        var last = sprites.Last();
        Vector2 smallest = new Vector2(last.rect.width, last.rect.height);
        Texture2D tx = new Texture2D(size, size, TextureFormat.RGBA32, false);
        raw.texture = tx;
        foreach (Sprite item in sprites)
        {
            Page p = c.Add(new Vector2(item.rect.width, item.rect.height), smallest);
            if (p == null) continue;
            Graphics.CopyTexture(item.texture, 0, 0,
                (int)item.rect.x, (int)item.rect.y, (int)item.rect.width, (int)item.rect.height,
                tx, 0, 0, (int)p.rect.x, (int)p.rect.y);
            //tx.SetPixels((int)p.rect.x, (int)p.rect.y, (int)item.rect.width, (int)item.rect.height,
            //    item.texture.GetPixels());
            //tx.Apply();
            Rect r = new Rect(item.rect.position + p.rect.position, item.rect.size);
            dic[item].sprite = Sprite.Create(tx, r, Vector2.zero);
        }
    }

```