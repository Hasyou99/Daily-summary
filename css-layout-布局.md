### 居中布局 ###

一、水平居中

* 要求：子元素于父元素水平居中且其（子元素与父元素）宽度均可变。


```` javacript

 	<div class="parent">
  		<div class="child">Demo</div>
	</div>

````

1. inline-block + text-align 

		.child {
		    display: inline-block;
		  }
		  .parent {
		    text-align: center;
		  }
2. table + margin 

		.child {
		    display: table;
		    margin: 0 auto;
		  }
		
3. absolute + transform

		.parent {
		    position: relative;
		  }
		  .child {
		    position: absolute;
		    left: 50%;
		    transform: translateX(-50%);
		  }
4. flex + justify-content

		.parent {
		    display: flex;
		    justify-content: center;
		  }
5. table-cell + vertical-align
	
		.parent {
		    display: table-cell;
		    vertical-align: middle;
		  }
6. flex + align-items

		.parent {
		    display: flex;
		    align-items: center;
		  }

二、水平与垂直居中

* 要求： 子元素于父元素垂直及水平居中且其（子元素与父元素）高度宽度均可变。


	    <div class="parent">
		  <div class="child">Demo</div>
		</div>


1.inline-block + text-align + table-cell + vertical-align

	.parent {
	    text-align: center;
	    display: table-cell;
	    vertical-align: middle;
	  }
	  .child {
	    display: inline-block;
	  }
2.absolute + transform

	.parent {
	    position: relative;
	  }
	  .child {
	    position: absolute;
	    left: 50%;
	    top: 50%;
	    transform: translate(-50%, -50%);
	  }

3.flex + justify-content + align-items

	.parent {
	    display: flex;
	    justify-content: center;
	    align-items: center;
	  }

三、多列布局

* 一列定宽，一列自适应

		<div class="parent">
		  <div class="left">
		    <p>left</p>
		  </div>
		  <div class="right">
		    <p>right</p>
		    <p>right</p>
		  </div>
		</div>

1.float + margin

	.left {
	    float: left;
	    width: 100px;
	  }
	  .right {
	    margin-left: 100px
	    /*间距可再加入 margin-left */
	  }

2.float + overflow
	
	.left {
	    float: left;
	    width: 100px;
	  }
	  .right {
	    overflow: hidden;
	  }

3.table

	.parent {
	    display: table;
	    width: 100%;
	    table-layout: fixed; //table 的显示特性为每列的单元格宽度合一定等与表格宽度。 table-layout: fixed; 可加速渲染，也是设定布局优先。
	  }
	  .left {
	    display: table-cell;
	    width: 100px;
	  }
	  .right {
	    display: table-cell;
	    /*宽度为剩余宽度*/
  	}

4.flex

	.parent {
	    display: flex;
	  }
	  .left {
	    width: 100px;
	    margin-left: 20px;
	  }
	  .right {
	    flex: 1;
	    /*等价于*/
	    /*flex: 1 1 0;*/
	  }	

* 两列定宽，一列自适应

````
		<div class="parent">
		  <div class="left">
		    <p>left</p>
		  </div>
		  <div class="center">
		    <p>center<p>
		  </div>
		  <div class="right">
		    <p>right</p>
		    <p>right</p>
		  </div>
		</div>
````

	.left, .center {
	    float: left;
	    width: 100px;
	    margin-right: 20px;
	  }
	  .right {
	    overflow: hidden;
	    /*等价于*/
	    /*flex: 1 1 0;*/
	  }