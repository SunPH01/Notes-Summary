直接上代码了，代码copy一下就可以看效果啦~~
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	<link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
	<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <title>Document</title>
    <style>
		html{
			font-size:625%;
		}
		body{
			background:#000;
		}
        /********************************多级菜单样式**************************/
        .dropdown:hover .menu-top {
            display: block;
        }
        .dropdown{
            position:relative;
        }
        .dropdown .hoverSpan{
            color:transparent;
            display:block;
            width:.80rem;
            height:.05rem;
            position:absolute;
            top:86%;
        }
        .dropdown-menu{
            top:90%;
        }
        .dropdown-submenu {
            position: relative;
        }

        .dropdown-submenu> .dropdown-menu {
            top: 0;
            left: 100%;
            margin-top: -.06rem;
            margin-left: -.01rem;
            -webkit-border-radius: 0 .06rem .06rem .06rem;
            -moz-border-radius: 0 .06rem .06rem .06rem;
            border-radius: 0 .06rem .06rem .06rem;
        }

        .dropdown-submenu:hover> .dropdown-menu {
            display: block;
        }

        .dropdown-submenu> span.more{
            display: block;
            position: absolute;
            right: .21rem;
            top: .04rem;
            width: 0; 
            height: 0;
            border-color: transparent;
            border-style: solid;
            border-width: .05rem 0 .05rem .05rem;
            border-left-color: #cccccc;
            margin-top: .05rem;
            margin-right: -0.10rem;
        }
        .dropdown-submenu:hover> a:after {
            border-left-color: #ffffff;
        }
        .dropdown-menu li i{
            position: absolute;
            font-size: .12rem;
            top: .07rem;
            left: .03rem;
        }
        .dropdown-menu li i:hover{
            color: #080808;
        }
        .dropdown  a:hover,.dropdown-name  a:hover{
            background-color:#2D7DDE!important;
            background-image: none!important;
            color: white!important;
        }
        .dropdown-submenu .pull-left {
            float: none;
        }
        .dropdown-submenu.pull-left> .dropdown-menu {
            left:94%;
            margin-left: .10rem;
            -webkit-border-radius: .06rem 0 .06rem .06rem;
            -moz-border-radius: .06rem 0 .06rem .06rem;
            border-radius: .06rem .06rem .06rem .06rem;
        }
    </style>
</head>
<body>

	<ul style="white-space: nowrap;">
		<li style="display:inline-block" class="dropdown">
			<a class="dropdown-toggle" role="button" href="javascript:void(0)">
				新闻<span class="caret"></span>
			</a>
			|
			<span class="hoverSpan"></span>
			<ul class="dropdown-menu menu-top">
				<li class="dropdown-submenu pull-left" style="width:100%;">
					<a class="dropdown-name" tabindex="-1">热门</a>
					<span class="more"></span>
					<ul class="dropdown-menu ">
						<li class="pull-left" style="width:100%;position:relative">
							
							<a class="dropdown-name ">台风</a>
							<span class="more"></span>
						</li>
					</ul>
				</li>
				<li class="dropdown-submenu pull-left" style="width:100%;">
					
					<a class="dropdown-name" tabindex="-1">推荐</a>
					<span class="more"></span>
					<ul class="dropdown-menu ">
						<li class="pull-left" style="width:100%;position:relative">
							
							<a class="dropdown-name ">娱乐</a>
							<span class="more"></span>
						</li>
					</ul>
				</li>
			</ul>
		</li>
	</ul>

</body>
</html>
```