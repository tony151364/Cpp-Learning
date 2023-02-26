## UE4
### 2.26
- 在头文件对以下代码编译时报错：Bad function definition
```C++
	void AMyFloorSwitch::OnOverlapBegin(
		UPrimitiveComponent* OverlappedComponent,
		AActor* OtherActor,
		UPrimitiveComponent* OtherComp,
		int32 OtherBodyIndex,
		bool bFromSweep,
		const FHitResult& SweepResult);
```
- 原因：qualified name is not allowed in member declaration
- 总结：这是C++的一个语法格式问题。UE下的VS不会直接提示错误，但是在正常环境下在编写时就会提示错误。所以UE的一些错误可能是C++语法错误，要两个环境下都试一下，方便区分。
