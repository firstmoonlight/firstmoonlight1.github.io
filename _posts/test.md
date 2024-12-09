## getTrailingObject的设计

1. 这个函数的目的是为了获取每个Type的起始位置，因此其入参应该是一个type，所以我们通过template作为其的入参选择。
2. 为了更好的封装性，我们通过使用getTrailingObjectImpl这样的形式，使其能够更好地隐藏其内部实现。
3. 对于不同的type，getTrailingObjectImpl应该有不同的实现，因为不同的type，其元素数量不同，且起始位置不同。但是由于模板类的模板成员函数无法进行特化，所以对于getTrailingObjectImpl我们通过`OverloadToken`作为入参来识别不同的type。
4. 细节部分：在Derived Class调用的时候使用this，确保我们调用的是Derived Class的getTrailingObjetcsImpl，而不是Base Class的。
5. 要想获取得到当前的Type所在的地址，那么就需要根据PrevType的地址，因此就形成了一个向前递归的流程。在每一个继承的层级中，我们都有一个函数`getTrailingObjectImpl(BaseTy *Obj, OverloadToken<NextTy>)`，表示获取得到当前的类型的首地址，即`NextTy`的首地址。因此为了计算得到`NextTy`的地址，那么我们就需要得到它的`PrevTy`的地址，正好是其子类的`getTrailingObjectImpl(BaseTy *Obj, OverloadToken<PrevTy>)`，再加上其个数`callNumTrailingObjects(BaseTy *Obj, OverloadTokeken<PrevTy>)`，就得到了PrevTy的最末尾的地址了。
