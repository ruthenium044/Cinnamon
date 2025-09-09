## Min and Max check templates

Naive approach, requires same type parameters

```CPP
template<typename T>
inline T max(T a, T b)
{
	return a > b ? a : b;
}

template<typename T>
inline T min(T a, T b)
{
	return a > b ? b : a;
}
```

Using auto

```CPP
template<typename T, typename U>
inline auto max(T a, U b)
{
    return a > b ? a : b;
}
```
