2020-08-17_09:45:29

# Loading assets

In a Actor or Component constructor (possibly other classes as well), an asset should be loaded with

```c++
#include "UObject/ConstructorHelpers.h"

MyClass::MyClass()
{
	static ConstructorHelpers::FObjectFinder<UMyAsset> Finder(
        TEXT("/Game/Folder/AssetName"));
	UMyAsset* MyAsset =
			Finder.Succeeded()
				? Cast<UMyAsset>(Finder.Object)
				: <fallback or nullptr>;
}
```

It is common that the target, `MyAsset` in the above example, is a member `UPROPERTY` and not a local variable.


Outside of a constructor we're not allowed to use `ConstructorHelpers`.
Instead use `StaticLoadObject`:

```c++
const TCHAR* AssetPath =
			TEXT("<Type>'/Game/Folder/<Name>.<Name>'");
UObject* LoadResult = StaticLoadObject(
    UMyAsset::StaticClass(), nullptr, AssetPath);
if (LoadResult == nullptr)
{
	// Error: The asset does not exist.
	return;
}
UType* Asset = Cast<UType>(LoadResult);
if (Asset == nullptr)
{
	// Error: The asset wasn't of the correct type.
    // Not sure if this can happen when including the '<Type>' part
    // of the asset path. But that part is optional, so just in case.
    return;
}
// Safe to use Asset here.
```