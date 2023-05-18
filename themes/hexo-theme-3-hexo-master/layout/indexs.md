# Why Blog
## 对博客的理解

<div id="musicMouseDrag" style="position:fixed; z-index: 9999; bottom: 0; right: 0;">
    <div id="musicDragArea" style="position: absolute; top: 0; left: 0; width: 100%;height: 10px;cursor: move; z-index: 10;"></div>
    <div >
    <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=38592976&auto=1&height=66"></iframe>
    <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=449577636&auto=1&height=66"></iframe>
    </div>
</div>
<script>
    var $DOC = $(document)
    $('#musicMouseDrag').on('mousedown', function (e) {
      // 阻止文本选中
      $DOC.bind("selectstart", function () {
        return false;
      });
      $('#musicDragArea').css('height', '100%');
      var $moveTarget = $('#musicMouseDrag');
      $moveTarget.css('border', '1px dashed grey')
      var div_x = e.pageX - $moveTarget.offset().left;
      var div_y = e.pageY - $moveTarget.offset().top;
      $DOC.on('mousemove', function (e) {
        var targetX = e.pageX - div_x;
        var targetY = e.pageY - div_y;
        targetX = targetX < 0 ? 0 : (targetX + $moveTarget.outerWidth() >= window.innerWidth) ? window.innerWidth - $moveTarget.outerWidth() : targetX;
        targetY = targetY < 0 ? 0 : (targetY + $moveTarget.outerHeight() >= window.innerHeight) ? window.innerHeight - $moveTarget.outerHeight() : targetY;
        $moveTarget.css({'left': targetX + 'px', 'top': targetY + 'px', 'bottom': 'inherit', 'right': 'inherit'})
      }).on('mouseup', function () {
        $DOC.unbind("selectstart");
        $DOC.off('mousemove')
        $DOC.off('mouseup')
        $moveTarget.css('border', 'none')
        $('#musicDragArea').css('height', '10px');
      })
    })
</script>