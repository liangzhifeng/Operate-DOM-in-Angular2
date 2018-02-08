# Operate-DOM-in-Angular2

实际Angular2项目中，大部分场景下都能很好利用双向绑定满足HTML元素的更新，
少部分场景需要直接操作DOM或动态修改Style，这时常规的插值、绑定、ngStyle和ngClass等手段可能满足不了你的需求。

那就试试以下几种方法吧：

# 一、          通过Angular2获取原生DOM元素, 在获取DOM元素后，你就可以愉快地直接操作DOM或者修改Style了

import {ElementRef} from '@angular/core';
   constructor(private el: ElementRef) {}

   this.el.nativeElement.querySelector('#test’)  // 获取当前Angular组件的后代元素中第一个id为test元素, 如不存在返回null
   this.el.nativeElement.querySelectorAll('.test')  // 获取获取当前Angular组件的后代元素所有class为test元素, 如不存在返回空的NodeList对象

注意：该选择器是在当前组件的后代元素中寻找，与jQuery的选择器$(‘cssSelector’)是不同的。



# 二、          通过nativeElement修改Style
this.el.nativeElement.querySelector('#test’).style // 字符串操作或property赋值 
this.el.nativeElement.querySelector('#test’).className  // class赋值
this.el.nativeElement.querySelector('#test’).classList  // 支持removeClass和addClass 使用起来比较方便，跟jQuery的addClass removeClass一样好用。

# 三 、     使用DomSanitizer

有些场景css是动态变化的，跟业务逻辑的变量相关，这时无法在CSS文件中定义了。
import {DomSanitizer} from '@angular/platform-browser';
constructor(private _sanitizer: DomSanitizer) {}
 getTemperatureImageStyle(node){
    return this._sanitizer.bypassSecurityTrustStyle("color:#f7f2f2; position:absolute;left:" + node.x + "%;top:" + node.y + "%;transform:translate(-" + node.x + "%,-" + node.y + "%)");
}

HTML中:   
<div *ngFor="let node of irTemperatureList">
<div  [style]="getTemperatureImageStyle(node)">
  {{node.temperature}}
  <i class="tip"></i>
</div>



Angular设计的确很友好，默认开启了防XSS攻击，我们在HTML中给style注入变量，Angular可能会报类似下面的错误/警告：

       WARNING: sanitizing Style stripped some content (see http://g.co/ng/security#xss)

 
  这时为了使Angular知道我们的变量是安全的，就需要使用Angular提供的DomSanitizer Service，一共有以下API

sanitizer.bypassSecurityTrustHtml(html);
sanitizer.bypassSecurityTrustScript(script);
sanitizer.bypassSecurityTrustStyle(style);
sanitizer.bypassSecurityTrustUrl(url);
sanitizer.bypassSecurityTrustResourceUrl(rurl);


 #  四 、放大招jQuery

jQuery可以作为没有好办法时的办法，用法很简单
import * as $ from "jquery";

如果不是不得不引入了第三方jQuery插件，不建议在Angular中直接使用jQuery，特别是Mobile项目不建议用。
在Mobile项目中我们要慎重考虑引入几十K库带来的性能影响。
