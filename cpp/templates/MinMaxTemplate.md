## Min and Max check templates

Naive approach, requires same type parameters

```CPP
#ifndef max
template<typename T>
inline T max(T a, T b)
{
	return a > b ? a : b;
}
#endif // !max

#ifndef min
template<typename T>
inline T min(T a, T b)
{
	return a > b ? b : a;
}
#endif // !min
```

