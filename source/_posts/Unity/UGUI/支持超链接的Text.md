---
title: 支持超链接的Text
author: 灵
comment: 'on'
tags:
  - Unity
  - UGUI
category:
  - Unity
  - UGUI
date: 2023-07-03 10:36:17
---

``` csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Text.RegularExpressions;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.EventSystems;
using UnityEngine.UI;

/// <summary>
/// 文本控件,支持超链接
/// </summary>
public class HyperlinkText : Text, IPointerClickHandler
{
    /// <summary>
    /// 超链接类
    /// </summary>
    private class HyperlinkInfo
    {
        /// <summary>
        /// 超链接的字符串的开始字符的第一个顶点的索引
        /// </summary>
        public int startIndex;


        /// <summary>
        /// 超链接的字符串的最后字符的最后一个顶点的索引
        /// </summary>
        public int endIndex;

        public string linkUrl;

        /// <summary>
        /// 超链接的覆盖区域
        /// </summary>
        public readonly List<Rect> textValidAreas = new List<Rect>();
    }

    /// <summary>
    /// 解析完最终的文本
    /// </summary>
    private string colorAddedStr;

    /// <summary>
    /// 超链接信息列表
    /// </summary>
    private readonly List<HyperlinkInfo> hrefInfos = new List<HyperlinkInfo>();

    /// <summary>
    /// 文本构造器
    /// </summary>
    protected static readonly StringBuilder textRebuild = new StringBuilder();

    [Serializable]
    public class HrefClickEvent : UnityEvent<string> { }

    [SerializeField]
    private HrefClickEvent onHrefClickCB = new HrefClickEvent();

    /// <summary>
    /// 超链接点击事件
    /// </summary>
    public HrefClickEvent onHrefClick
    {
        get { return onHrefClickCB; }
        set { onHrefClickCB = value; }
    }

    /// <summary>
    /// 每个字符的面片有4个顶点
    /// </summary>
    const int perCharVerCount = 4;

    const string pattern = @"<a href=([^>\n\s]+)>(.*?)(</a>)";

    const string colorStartStr = "<color=#01EAFFFF>";

    const string colorEndStr = "</color>";

    /// <summary>
    /// 超链接正则
    /// </summary>
    private static readonly Regex s_HrefRegex =
        new Regex(pattern, RegexOptions.Singleline);

    private HyperlinkText mHyperlinkText;



    protected override void Awake()
    {
        base.Awake();
        mHyperlinkText = GetComponent<HyperlinkText>();
    }


    protected override void OnEnable()
    {
        base.OnEnable();
        mHyperlinkText.onHrefClick.AddListener(OnHyperlinkTextInfo);
    }

    protected override void OnDisable()
    {
        base.OnDisable();
        mHyperlinkText.onHrefClick.RemoveListener(OnHyperlinkTextInfo);
    }


    public override void SetVerticesDirty()
    {
        base.SetVerticesDirty();
#if UNITY_EDITOR
        if (UnityEditor.PrefabUtility.GetPrefabType(this) == UnityEditor.PrefabType.Prefab)
        {
            return;
        }
#endif
     
        colorAddedStr = GetDealedText(text);


    }

    protected override void OnPopulateMesh(VertexHelper toFill)
    {
        print("OnPopulateMesh ");

        var orignText = m_Text;
        m_Text = colorAddedStr;
        base.OnPopulateMesh(toFill);

        m_Text = orignText;


        //UGUI的网格处理类
        UIVertex vert = new UIVertex();

        // 重新创建超链接包围框
        foreach (var hrefInfo in hrefInfos)
        {
            hrefInfo.textValidAreas.Clear();

            //toFill.currentVertCount表示当前Text组件的所有网格的最后一个网格的顶点索引
            //hrefInfo.startIndex大于它 是startIndex大于它运算出错了 运算出错 则跳过包围框生成
            if (hrefInfo.startIndex >= toFill.currentVertCount)
            {
                continue;
            }

            //获取startIndex所指示的顶点
            toFill.PopulateUIVertex(ref vert, hrefInfo.startIndex);

            var pos = vert.position;
            var bounds = new Bounds(pos, Vector3.zero);

            for (int i = hrefInfo.startIndex, m = hrefInfo.endIndex; i < m; i++)
            {
                if (i >= toFill.currentVertCount)
                {
                    break;
                }

                toFill.PopulateUIVertex(ref vert, i);
                pos = vert.position;

                if (pos.x < bounds.min.x || pos.y < bounds.min.y) // 换行重新添加包围框
                {
                    hrefInfo.textValidAreas.Add(new Rect(bounds.min, bounds.size));
                    bounds = new Bounds(pos, Vector3.zero);
                }
                else
                {
                    bounds.Encapsulate(pos); // 扩展包围框
                }
            }
            hrefInfo.textValidAreas.Add(new Rect(bounds.min, bounds.size));
        }


    }






    /// <summary>
    /// 生成超链接文本的显示区域矩形和链接信息
    /// 用于在点击的时候判断是否点击中了超链接
    /// </summary>
    /// <returns>  
    /// 返回超链接解析后的最后输出文本
    /// 即在原字符串的基础上用颜色标签的文本
    /// 代替<a href=xx><\a>这样text组件识别不了的标签</returns>
    protected virtual string GetDealedText(string originText)
    {
        textRebuild.Clear();
        hrefInfos.Clear();

        //startIndex表示上次匹配正则的字符串的最后字符的索引+1
        //例如这段话
        //aa<a href=666>[本篇博客]</a>aa
        //第一次匹配到的字符串是<a href=666>[本篇博客]</a>
        //则它最后的字符在整个字符串的索引是24
        //则startIndex在这时候为24+1即倒数第二个a的索引
        var matchNextCharIndex = 0;

        // 实际显示中 尖括号里面的是不显示的
        // 上面那句话里面实际显示的就aa 和[本篇博客]
        // 这些字符的最后字符]的索引是8
        // 所以actualStartIndex表示的下次匹配的开始索引是9
        var actualNextCharIndex = 0;
       
        // 在第一个匹配正则式的字符串之前的字符串
        // 或者这次匹配正则式的字符串与
        // 上次匹配的字符串之间的字符串
        var beforeStr = string.Empty;

        //beforeStr的清空所有空白符形式
        var beforeStrNoBlank = string.Empty;

        //这次匹配到的字符串之前的字符串的最后一个字符的索引
        //例如aa<a href=666>[本篇博客]</a>aa
        //第一次匹配的时候 beforeStrLastCharIndex是上面字符串第二个a的索引
        var beforeStrLastCharIndex = 0;


        // match.Groups[0]是常量pattern字符串匹配到的所有字符串
        // match.Groups[1]是常量pattern字符串里面第一个小括号匹配到的字符串
        // match.Groups[2]是常量pattern字符串里面第二个小括号匹配到的字符串
        // 依次类推

        foreach (Match match in s_HrefRegex.Matches(originText))
        {

            beforeStr = originText.Substring(matchNextCharIndex,
                match.Index - matchNextCharIndex);

            //将firstStr里面的所有空白符（包括'\n''\t'' '等）去掉
            //这是因为空白字符不参与字体的网格生成  
            //超链接字符前面空白字符不能纳入超链接的可点击区域计算
            //否则生成的点击区域会有问题
            beforeStrNoBlank = Regex.Replace(beforeStr, @"\s", "");

            textRebuild.Append(beforeStr);
            textRebuild.Append(colorStartStr);  // 超链接颜色

            var url = match.Groups[1];
            var hyperText = match.Groups[2];
            beforeStrLastCharIndex = (beforeStrNoBlank.Length + actualNextCharIndex);
            DebugFunc(actualNextCharIndex, beforeStr, beforeStrNoBlank, match, url);

            var hrefInfo = new HyperlinkInfo
            {
                //因为 后面要根据startIndex和endIndex去获取实际的顶点，如果这两变量
                //纳入了空白符的计算 会导致变量偏大，从而获取的顶点是后面的字的顶点
                startIndex = beforeStrLastCharIndex * perCharVerCount,

                endIndex = (beforeStrLastCharIndex + hyperText.Length ) * perCharVerCount,

                linkUrl = url.Value
            };

            hrefInfos.Add(hrefInfo);

            textRebuild.Append(hyperText.Value);
            textRebuild.Append(colorEndStr);


            matchNextCharIndex = match.Index + match.Length;

            actualNextCharIndex += beforeStrNoBlank.Length + match.Groups[2].Length;
        }

        //将最后一次匹配之后的字符串加上去
        textRebuild.Append(originText.Substring(matchNextCharIndex, originText.Length - matchNextCharIndex));

        return textRebuild.ToString();
    }

    private static void DebugFunc(int actualStartIndex, string beforeStr, string beforeStrNoBlank, Match match, Group group)
    {
        string str = " HyperlinkText GetOutputText ";

        for (int i = 0; i < match.Groups.Count; i++)
        {
            str += (" match.Groups[i]   " + i + "   " +
                   match.Groups[i] + " match.Length " + match.Length + "\n");
        }

        print(str + " HyperlinkText GetOutputText  firstStr " + beforeStr +
            "   " + match.Groups[2] +
            " group.Value " + group.Value +
            " actualStartIndex " + actualStartIndex +
            "  firstStrNoBlank.Length " + beforeStrNoBlank.Length +
            " match.Index " + match.Index);
    }

    /// <summary>
    /// 点击事件检测是否点击到超链接文本
    /// </summary>
    /// <param name="eventData"></param>
    public void OnPointerClick(PointerEventData eventData)
    {
        Vector2 lp = Vector2.zero;

        //将点击点从屏幕坐标系转换到rectTransform的局部坐标系
        RectTransformUtility.ScreenPointToLocalPointInRectangle(rectTransform,
            eventData.position, eventData.pressEventCamera, out lp);

        foreach (var hrefInfo in hrefInfos)
        {
            var boxes = hrefInfo.textValidAreas;
            for (var i = 0; i < boxes.Count; ++i)
            {
                if (boxes[i].Contains(lp))
                {
                    onHrefClickCB.Invoke(hrefInfo.linkUrl);
                    return;
                }
            }
        }
    }
    /// <summary>
    /// 当前点击超链接回调
    /// </summary>
    /// <param name="info">回调信息</param>
    private void OnHyperlinkTextInfo(string info)
    {
        Debug.Log("超链接信息：" + info);
    }

}
```


