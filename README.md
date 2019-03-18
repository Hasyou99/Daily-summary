## javaScript函数节流与防抖之区别


函数防抖(debounce)与函数节流(throttle)都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟、假死或卡顿的现象。

   一、 函数防抖(debounce)

   - 概念

    函数防抖（debounce）是指在一定时间内，动作被连续频繁触发的情况下，动作只会被执行一次，也就是说在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。
     
   - 应用场景
   
		对于函数防抖，主要应用场景为以下几种：

		1. 给按钮提交函数防抖阻止表单多次提交
		2. 对于输入框连续输入进行AJAX验证时，用函数防抖能有效减少请求次数
		3. 判断scroll是否滑到底部，滚动事件+函数防抖
		4. 表单验证（用户输入时，验证输入的正确性）

		-总结来说，函数防抖适合多次事件一次响应的情况


   - 源码
	
		function debounce(fn, wait) {
		  var timer = null;
		  return function () {
		      var context = this
		      var args = arguments
		      if (timer) {
		          clearTimeout(timer);
		          timer = null;
		      }
		      timer = setTimeout(function () {
		          fn.apply(context, args)
		      }, wait)
		  }
		};
		var fn = function () {
		  console.log('boom')
		}
		setInterval(debounce(fn,500),1000) // 第一次在1500ms后触发，之后每1000ms触发一次
		setInterval(debounce(fn,2000),1000) // 不会触发一次（我把函数防抖看出技能读条，如果读条没完成就用技能，便会失败而且重新读条）
   

 - 简单实例
	
	在我们项目中搜索框，存在用户输入文本，自动联想匹配出一些接口供用户选择，我们可能首先想到的做法是监听输入框的keyup事件，然后异步去查询结果。但是存在这样的情况，如果用户连续输入n多个字符，那么瞬间就会触发10多次请求，造成请求浪费。
	我们想要的是用户停止输入的时候才去触发查询的请求，这时候防抖函数就可以帮到我们。

	具体代码实现如下
	
		请输入搜索词：<input type="text" id="search" list="search_list" name="">
   		<datalist id="search_list"></datalist>
    
        (function ($) {
            var $search = $('#search');
            var $search_list = $('#search_list');
            var timer = null;  //定义定时器
            $search.on('keyup', function () {
                timer && clearTimeout(timer); //如果定时器存在，清除定时器，中断操作
                timer = setTimeout(handleChange, 500); //发送异步请求
            });
			//输入字段改变
            function handleChange() {
                var s = $search.val();
                $search_list.empty();
                fetchBaiduSuggest(s, function (word, result) {
                    result.map(function (w) {
                        $('<option />').val(w).text(w).appendTo($search_list);
                    });
                });
            }

            function fetchBaiduSuggest(word, cb) {
                $.getJSON("http://suggestion.baidu.com/su?wd=" + encodeURIComponent(word) + "&cb=?", function (
                    data) {
                    if ($.isArray(data.s)) {
                        cb(word, data.s);
                    }
                });
            }
        })(jQuery);
	

	二、函数节流
	
	- 概念
	
	函数节流是指一定时间内执行的操作只执行一次，也就是说即预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执行该动作，然后进入下一个新周期，一个比较形象的例子是如果将水龙头拧紧直到水是以水滴的形式流出，那你会发现每隔一段时间，就会有一滴水流出。

	- 应用场景
	
		- DOM 元素的拖拽功能实现（mousemove）
		- 射击游戏的 mousedown/keydown 事件（单位时间只能发射一颗子弹）
		- 计算鼠标移动的距离（mousemove）
		- Canvas 模拟画板功能（mousemove）
		- 搜索联想（keyup）
		- 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果是 throttle 的话，只要页面滚动就会间隔一段时间判断一次
		

	- 源码
	
			function throttle(fn, gapTime) {
			  let _lastTime = null;
			
			  return function () {
			    let _nowTime =  new Date()
			    if (_nowTime - _lastTime > gapTime || !_lastTime) {
			      fn();
			      _lastTime = _nowTime
			    }
			  }
			}
			
			let fn = ()=>{
			  console.log('boom')
			}
			
			setInterval(throttle(fn,1000),10)


	- 简单实例
	
	如果我们现在需要做一个记录用户鼠标移动轨迹的小应用，像这种对流畅度有一定的要求的情况，再用上面的防抖函数就不合适了，如果用防抖函数会出现什么效果呢？如果鼠标移动速度较快，那么只有在我们每次鼠标停下来的时候才能发送当前的位置，但是如果我们每次移动都发送并记录鼠标的位置，那也是相当恐怖的，鼠标随便移动一下就会有成百上千条记录位置的请求发出去，这个时候我们就可以用到函数节流，节流顾名思义也就是个一段儿时间去发送一次位置，可以大大减少请求发送次数。

		<h2>函数节流</h2>
	    <div>
	        X: <span id='x'></span>
	        Y: <span id='y'></span>
	    </div>
	    <script>
	    (function($){
	        var $x = $('#x');
	        var $y = $('#y');
	        var isRun = true;
	
	        $(document).on('mousemove',function(event){
	            var e = event || window.event;
	            if(!isRun) return; // 判断是否已空闲，如果在执行中，则直接return
	            isRun = false;
	            setTimeout(function(){
	                $x.text(e.pageX);
	                $y.text(e.pageY);
	                isRun = true;
	            },100)
	        })
	    })(jQuery);

	
	三、总结

	函数防抖和函数节流是在时间轴上控制函数的执行次数。函数节流和函数去抖的核心其实就是限制某一个方法被频繁触发，而一个方法之所以会被频繁触发，大多数情况下是因为 DOM 事件的监听回调，而这也是函数节流以及去抖多数情况下的应用场景

	[github地址：https://github.com/Hasyou99/Daily-summary](https://github.com/Hasyou99/Daily-summary)