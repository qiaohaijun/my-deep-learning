这里需要注明的是，我不确定，是否这里的c包括c++。

---

ctypes提供的类型

1. c_char
2. c_wchar
3. c_ubyte
4. c_ushort
5. c_uint
6. c_long
7. c_longlong
8. c_ulonglong
9. c_float
10. c_double
11. c_longdouble
12. c_char_p
13. c_wchar_p
14. c_void_p

---

重点是这几个特殊的

1. c_longdouble
2. c_*_p

---

1. c_longdouble 的c语言类型是long double
2. c_char_p char *(NULL-terminated)
3. c_void_p void *
