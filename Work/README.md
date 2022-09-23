#### 教训
- [ ] 任务可能是让我先看懂代码，而不是急着做出来
- [ ] 编码前一定要确定方案
- [ ] 一个地方卡了1~2个小时，就要停下来，试着寻求他人的帮助
- [ ] 每个函数只做一件事
- [ ] for比while好，阅读时while判断越界比for难一点

#### 待解决:
- [ ] 翻译功能的const char *
- [ ] 双层GroupBox点击两次
- [x] 堆栈找不到物品名字的数据
- [x] fade效果怎么做: 在item_bar中已实现。
- [x] 宏能不能用inline切换：可以的，效果该不错

- [ ] 能不能主动调用回调函数 
```C++
GetCore()->ExecCallback(this, "on_item_bar_select_changed",
		CVarList() << new_index << old_index);
```

- [x] tinyxml最外层是否只能有一个
- [x] 连按覆盖以前效果：map->erase

- [ ] 这个代码的含义
```C++
TAMEFOOD_VTOR::iterator itr_itmfind = std::find_if(tamedata.Foods.begin(), tamedata.Foods.end(),
			[tt](TAMEFOOD item) {return strcmp(tt, item.ID.c_str()) == 0; });
// tame_module.cpp 的 line 987
```
- [ ] 阅读玩家控制器代码
- [ ] Character -> Pawn -> GameObject ?
	- state machine
	- GameObject
	
#### 我发现的bug
- [ ] 骑乘蛤蟆人物变大
- [x] 恐龙出现时，出现提示框，这时候打开背包，等到恐龙走了，快捷栏会出现
- [ ] 非食物驯养，原因在于服务器TameModuleX::DoFeedNpcFood流程判断错误
- [x] 吃食物会切换手中武器
