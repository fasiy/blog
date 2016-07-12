<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-17
  title: ajax改请求头
  tags:ajax,爬虫
  category:技术笔记
  status:publish
  summary:ajax提交中如何改http报文的头信息
-->

$.ajax({ url: this.href, dataType: "text", beforeSend: function(req) { req.setRequestHeader("FooHeader", "foo"); }, success: function(form) { MvcUtil.showSuccessResponse(form, link); }, error: function(xhr) { MvcUtil.showErrorResponse(xhr.responseText, link); }});
