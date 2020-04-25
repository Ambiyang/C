### define a pointer function pFunc whose type is bool(*)(int)
`typedef bool(*pFunc)(int);`  
- how to use  
```
pFunc p1 = (pFunc)0xD0001111;  
p1(1);  //p1->()  
(*p1)(1);  //p1->*->()  
*p1(1); //this is wrong p1->()->*
```

### To understand pointer
assume this is memory  
```
Addr　　Value  
1001　|　　1 |  
1000　| 1001 |  
```
*p = (*int)1000  
then  
p =1001 &p=1000 *p=1
