# Chandler's Optimizer Hack

```C++
void escape(void* p)
{
	asm volatile("" : : "g"(p) : "memory");
}
```
