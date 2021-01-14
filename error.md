sscanf (s_PC_Uart.value, "%d", u_OPT_LIST.st.c_many); %d infers int which will copy 4bytes to des.  
okada bugged at here cuz u_OPT_LIST.st.c_many only has 2 bytes.the remaining 2 bytes overflowed.

printf ("%d\n", u_OPT_LIST.st.c_many); address will be printed

printf %md/%0md only add don't truncate 

scanf 
scanf("%d%d",&a,&b);input can be separated by space or CR  

scanf("%c%c",&a,&b); input:c //a=c,b=
scanf("%c %c",&a,&b);##因此可以看出%c会把空格按字符读入，其他方式不会。## input:c b//a=c,b=b

scanf("%s",str);##%s读入的时候以空格跟换行作为读入结束的标志##


sprintf

char a1[] = {'A', 'B', 'C', 'D', 'E', 'F', 'G'};  
char a2[] = {'H', 'I', 'J', 'K', 'L', 'M', 'N'};
sprintf(s, "%s%s", a1, a2); //Don't do that!
十有八九要出问题了。是否可以改成：
sprintf(s, "%7s%7s", a1, a2);
也没好到哪儿去，正确的应该是：
sprintf(s, "%.7s%.7s", a1, a2);//产生："ABCDEFGHIJKLMN"
这可以类比打印浮点数的”%m.nf”，在”%m.ns”中，m 表示占用宽度（字符串长度不足时补空
格，超出了则按照实际宽度打印），n 才表示从相应的字符串中最多取用的字符数。

sscanf("123456","%s",str);//---------str的值为 "123456\0!!!"
    //这个实验很简单，把源字符串"123456"拷贝到str的前6个字符，并且把str的第7个字符设为null字符，也就是\0
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("123456","%3s",str); //---------str的值为 "123\0!!!!!!"
    //看到没有，正则表达式的百分号后面多了一个3，这告诉sscanf只拷贝3个字符给str，然后把第4个字符设为null字符。
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("aaaAAA","%[a-z]",str);// ---------str的值为 "aaa\0!!!!!!"
    //从这个实验开始我们会使用正则表达式，括号里面的a-z就是一个正则表达式，它可以表示从a到z的任意字符，
    //在继续讨论之前，我们先来看看百分号表示什么意思，%表示选择，%后面的是条件，比如实验1的"%s"，s是一个条件，表示任意字符，"%s"的意思是：只要输入的东西是一个字符，就把它拷贝给str。实验2的"%3s"又多了一个条件：只拷贝3个字符。实验3的“%[a-z]”的条件稍微严格一些，输入的东西不但是字符，还得是一个小写字母的字符，所以实验3只拷贝了小写字母"aaa"给str，别忘了加上null字符。
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("AAAaaaBBB","%[^a-z]",str);// ---------str的值为 "AAA\0!!!!!!"
    //对于所有字符，只要不是小写字母，都满足"^a-z"正则表达式，符号^表示逻辑非。前3个字符都不是小写字符，所以将其拷贝给str，但最后3个字符也不是小写字母，为什么不拷贝给str呢？这是因为当碰到不满足条件的字符后，sscanf就会停止执行，不再扫描之后的字符。
    cout<<str<<endl;
 
    /*
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("AAAaaaBBB","%[A-Z]%[a-z]",str);// ---------段错误
    //这个实验的本意是：先把大写字母拷贝给str，然后把小写字母拷贝给str，但很不幸，程序运行的时候会发生段错误，因为当sscanf扫描到字符a时，违反了条件"%[A-Z]"，sscanf就停止执行，不再扫描之后的字符，所以第二个条件也就没有任何意义，这个实验说明：不能使用%号两次或两次以上
    cout<<str<<endl;
    */
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("AAAaaaBBB","%*[A-Z]%[a-z]",str); //---------str的值为 "aaa\0!!!!!!"
    //这个实验出现了一个新的符号：%*，与%相反，%*表示过滤满足条件的字符，在这个实验中，%*[A-Z]过滤了所有大写字母，然后再使用%[a-z]把之后的小写字母拷贝给str。如果只有%*，没有%的话，sscanf不会拷贝任何字符到str，这时sscanf的作用仅仅是过滤字符串。
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("AAAaaaBBB","%[a-z]",str);// ---------str的值为 "!!!!!!!!!!"
    //做完前面几个实验后，我们都知道sscanf拷贝完成后，还会在str的后面加上一个null字符，但如果没有一个字符满足条件，sscanf不会在str 的后面加null字符，str的值依然是10个惊叹号。这个实验也说明了，如果不使用%*过滤掉前面不需要的字符，你永远别想取得中间的字符。
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    sscanf("AAAaaaBC=","%*[A-Z]%*[a-z]%[^a-z=]",str); //---------str的值为 "BC\0!!!!!!!"
    //这是一个综合实验，但这个实验的目的不是帮我们复习前面所学的知识，而是展示两个值得注意的地方：
    //注意1：%只能使用一次，但%*可以使用多次，比如在这个实验里面，先用%*[A-Z]过滤大写字母，然后用%*[a-z]过滤小写字母。
    // 注意2：^后面可以带多个条件，且这些条件都受^的作用，比如^a-z=表示^a-z且^=(既不是小写字母，也不是等于号)。
    cout<<str<<endl;
 
    for (int i = 0; i < 10; i++) str[i] = '!';
    int k;
    sscanf("AAA123BBB456", "%*[A-Z]%i", &k); //---------k的值为123
    //首先，%*[^0-9]过滤前面非数字的字符，然后用%i把数字字符转换成int型的整数，拷贝到变量k，注意参数必须使用k的地址。    cout<<str<<endl;
    cout<<k<<endl;
