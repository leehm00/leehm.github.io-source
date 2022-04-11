---
title: apple script使用
top: false
cover: false
toc: true
mathjax: true
tags:
  - null
categories:
  - null
typora-root-url: ../
date: 2022-03-22 17:27:00
password: 123456
summary:
---

```
on run {input, parameters}
	
	(* Your script goes here *)
	tell application "Google Chrome"
		activate
		try
			
			tell front window
				make new tab with properties {URL:"https://weixine.ustc.edu.cn/2020/login"}
				delay 3
			end tell
			tell window 1
				tell active tab
					execute javascript "document.getElementsByClassName('btn')[0].click()"
					delay 4
					
					-- execute javascript "document.getElementsByClassName('btn ripple bottom-box-button-area-1')[0].click()"
				end tell
			end tell
			tell window 1
				tell active tab
					execute javascript "document.getElementById('username').click()"
					delay 1
				end tell
			end tell
			tell application "System Events"
				keystroke return
				delay 1
			end tell
			tell window 1
				tell active tab
					delay 4
					execute javascript "document.getElementById('report-submit-btn-a24').click()"
				end tell
			end tell
		on error errMsg
			tell front window
				make new tab with properties {URL:"https://weixine.ustc.edu.cn/2020/home"}
				delay 3
			end tell
			tell window 1
				tell active tab
					delay 4
					execute javascript "document.getElementById('report-submit-btn-a24').click()"
				end tell
			end tell
		end try
	end tell
	return input
end run
```

btn ripple bottom-box-button-area-1

document.getElementsByClassName('btn ripple bottom-box-button-area-1')[0].childNodes

```
tell window 1
			tell active tab
				execute javascript "document.getElementById('username').click()"
				delay 1
			end tell
		end tell
		tell application "System Events"
			keystroke "PB18071495"
			delay 1
			keystroke return
			delay 1
		end tell
		tell window 1
			tell active tab
				execute javascript "document.getElementById('password').click()"
				delay 1
			end tell
		end tell
		tell application "System Events"
			keystroke "730717aaa"
			delay 1
			keystroke return
			delay 1
		end tell

```

点击控制台左上角的鼠标按钮，之后就可以按照看到样式，有不同的搜索方式

```shell
(*
	--通过ID获取元素（getElementById）tell tab
	--execute javascript "document.getElementById('vkeyIcon').click()"
	--通过类名获取元素（getElementsByClassName）
	--execute javascript "document.getElementsByClassName('identicon__address-wrapper')[0].click()"
	--通过name属性获取元素（getElementsByName）
	--execute javascript "document.getElementsByName('identicon__address-wrapper')[0].click()"
	--通过标签名获取元素（getElementsByTagName）
	--execute javascript "document.getElementsByTagName('identicon__address-wrapper')[0].click()"
	--?
	--execute javascript "document.querySelectorAll('.tile-grid .most-visited')[3].click()"
	--set active tab index to 3 --切换当前window中选中的标签 tell window
	--让input标签聚焦，即模拟点击input
	--execute javascript "document.getElementsByClassName('name-input')[0].focus()"
	--获取input元素的value值
	--set grabVar to execute javascript "document.getElementsByClassName('readonly-input__input')[0].value;" --可用，这种写法可以从外部接收到value值
	--通过JS中的DOM相关方法来获取文本节点的值 可以在Chrome控制台来尝试打印元素内容或者模拟click点击，Console控制台可以提示代码，非常方便
	set grabVar to execute javascript "document.getElementsByClassName('c-nav--footer__listitem--meta v--center')[0].childNodes[0].innerHTML" 
*)

```

例如搜索`document.getElementsByClassName('btn')[0].childNodes`查看具体信息

report-submit-btn-a24
