#### 


#### 教训
- 1.看代码最后过一遍，看看是不是都理解了
- 2.任务可能是让我先看懂代码，而不是急着做出来
- 3.任务开始时一定要确定方案，不要着急编码
- 4.任务结束时确定结果，之后再提交
- 5.一个地方卡了1个小时，就要停下来休息一下，还是无法解决载试着寻求他人的帮助
- 6.每个函数只做一件事，这样更便于阅读和管理。不要把过的的内容放到一个函数里面

#### 待解决:
- [x] 1.翻译功能的const char *：类似const char* 能自动转string，const wchar_t*能自动转wstring
- [ ] 2.wstring
- [ ] 3.双层GroupBox点击两次：猜测，会不会和7类似
- [x] 4.堆栈找不到物品名字的数据：在其他部分花费太多时间，导致错误物品数据加载的细节
- [x] 5.fade效果怎么做: 在item_bar中已实现。
- [x] 6.宏能不能用inline切换：可以的，效果还不错
- [x] 7.拖拽物品到快捷栏(Grid)很卡，设置Enable form true to false;
- [x] 8.能不能主动调用回调函数 
```C++
GetCore()->ExecCallback(this, "on_item_bar_select_changed",
		CVarList() << new_index << old_index); // 不能用，不知道为啥
		
GuiUtil_RunCallback(this, "on_selected", CVarList());  // 可以用
GetDelegateControl()->OnSuborEvent(this, event, args); // 子控件事件，我应该用不上
```
- [x] 9.tinyxml最外层是否只能有一个：不是的，可以后多个
- [x] 10.连按覆盖以前效果：map->erase
- [ ] 11.类图如何画
- [ ] 12.程序开发过程中为什么for的优先级比while高
- [ ] 13.四元数AngleToMatrix、解析几何
- [ ] 14.Assert、STL
- [ ] 15.SceneBox
- [ ] 16.光线追踪，硬件，可以看看之前那个装机视频

	
#### 我发现的bug
- [ ] 骑乘蛤蟆人物变大
- [x] 恐龙出现时，出现提示框，这时候打开背包，等到恐龙走了，快捷栏会出现
- [ ] 非食物驯养，原因在于服务器TameModuleX::DoFeedNpcFood流程判断错误
- [x] 吃食物会切换手中武器
