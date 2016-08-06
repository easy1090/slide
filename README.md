# slide
根据jquery slide自己写了一个slide,基于jquery的animation实现了banner轮播图，可以自动左右播放，也可左右滑动切换图片
<!DOCTYPE html>
<html>
    <head lang="zh-cn">
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=1" >
        <title>自适应轮播图（不同分辨率）</title>
        <script type="text/javascript" src="http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
        <style>
        	body{margin: 0px;padding:0;  }
            .list{   
		        overflow:hidden;  
		    }  
		    .list ul{   
		        margin:0;  
		        padding:0;  
		    }  
		    .list ul li{  
		        float:left;   
		    }  
        </style>

    <head>
    <body>
	    <div class="list" id = "slide">  
	        <ul>  
	            <li><img src="./images/1.png"  alt="1" /></li>  
	            <li><img src="./images/2.png"  alt="2" /></li>  
	        </ul>  
	    </div> 
	    <script>
	   $(document).ready(function() {
	    	/*根据图片数量设置css*/
	    	var width = $(window).width();
	    	var height = (978/1390)*width;
	    	var direction = "left";
	    	/*slide*/
	    	var slide = new Slide(width,height,direction);
	    	slide.init();
			function Slide(width,height,direction){
				var slide = {
					width:0,
	    			height:0,
	    			touchstart:0,
	    			touchend:0,
	    			timer:0,
	    			direction:direction||"right",
					init:function(){
						if(typeof width == undefined || typeof height == undefined){
							return;
						}
						this.width = width;
						this.height = height;
						var num = $("#slide ul li").length;
				    	/*容器宽度高度*/

				    	$("#slide").css("width",this.width + "px").css("height",this.height + "px");	    	
				    	$("#slide ul").css("width",num * this.width + "px").css("height",this.height + "px");
				    	
				    	$("#slide ul li img").css("width",this.width + "px").css("height",this.height + "px");
				    	
						
						this.auto();
						this.bind();
					},
					auto:function(){
						var _this = this;//绑定作用域
						var ulDom = $("#slide ul");//不变，所以尽量不操作 dom
						this.timer = setInterval(function(){
								if(_this.direction == "right"){
									_this.renderRight();
								}else{
									_this.renderLeft();
								}
						},2000);
					},
					stopauto:function(){
						clearInterval(this.timer);
					},
					bind:function(){
						var _this = this;//绑定作用域var ulDom = $("#slide ul");//不变，所以尽量不操作 dom
						$("#slide").on("touchstart",function(e){
							e.preventDefault();
							if (event.targetTouches.length == 1) {
     							var touch = event.targetTouches[0];
     							_this.touchstart = touch.clientX;
     						}
						});
						$("#slide").on("touchend",function(e){
							e.preventDefault();
							var ulDom = $("#slide ul");
							if (event.changedTouches.length == 1) {
     							var touch = event.changedTouches[0];
     							_this.touchend = touch.clientX;
     						}
     						//左
     						if(_this.touchend - _this.touchstart < -5){
     							console.log("left");
     							_this.renderLeft();
     							//重新开始自动播放
     							_this.stopauto();
     							_this.auto();
     						}
     						if(_this.touchend - _this.touchstart > 5){
     							console.log("right");
     							_this.renderRight();
     							//重新开始自动播放
     							_this.stopauto();
     							_this.auto();
     						}
						});
					},
					renderLeft:function(){
						var _this = this;//绑定作用域
						var ulDom = $("#slide ul");//不变，所以尽量不操作 dom
						ulDom.animate(															{
								'margin-left': '-'+_this.width +'px'
								},
								'slow',
								function(){
									//此处保证能循环轮播
									//将已经轮播过的节点的第一张图片添加到末尾的位置
									//再将margin-left重置为0px,为了能动画过渡，否则无动画;
									//这样就能一直的循环下去.$().after(dom),是替换操作，非dom是添加
     							ulDom.css({'margin-left':0}).children('li').first().before(ulDom.children('li').last());
								
								}

							);

					},
					renderRight:function(){
						var _this = this;//绑定作用域
						var ulDom = $("#slide ul");//不变，所以尽量不操作 dom
						ulDom.animate(
								{
								'margin-left': _this.width +'px'
								},
								'slow',
								function(){
									//此处保证能循环轮播
									//将已经轮播过的节点的第一张图片添加到末尾的位置
									//再将margin-left重置为0px,为了能动画过渡，否则无动画;
									//这样就能一直的循环下去.$().after(dom),是替换操作，非dom是添加
									ulDom.css({'margin-left':0}).children('li').last().after(ulDom.children('li').first());
								
								}
							);
					}
				}
				return slide;
			}
		});
	    </script> 
    </body>
</html>
