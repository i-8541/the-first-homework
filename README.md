# the-first-homework
成绩管理系统
/**************************
** 文件名:200110732_孙赫辰_实验七
** 创建人:孙赫辰
** 日 期:11.2
** 修改人:
** 日 期:
** 描 述:成绩管理系统V3.0
** 版 本: **------------------------
*************************/


#include<stdio.h>
#include<string.h>
#define N 30//总人数
#define M 10//姓名大小
#define K 6//课程总数

//Menu显示菜单并读入第一次选项
int Menu(void);
//ReadScore读入成绩以及学号和姓名并复制数据，并在读入数据时计算个人总分
void ReadScore(int num,int cnt,double Id[],double Id_c[],double Sub[][K],double Sub_c[][K],char Name[][M],char Name_c[][M],double Sum[]);
//Print按照输入顺序输出
void Print(double Sub_c[][K],double Id_c[],int cnt,int num,char Name_c[][M],int choice,double Sum[]);
//Average计算每门课程的平均分以及总分
void Average(double Sub[][K],int num,int cnt);
//Ascending Descending决定升序还是降序排列
int Ascending (double a,double b);
int Descending(double a,double b);
//Scoresort对成绩排序 降序或者升序 以及搜索姓名；choice用来判断是不是执行9：搜索姓名
void Scoresort(double Sum[],double Id[],int num,int (*p) (double,double),int choice,char Name[][M]);
//IDsort按照学号降序排列 并搜索学号
void IDsort(double Sum[],double Id[],int num,int (*p) (double,double),char Name[][M],int choice);
//Analysis计算每门课程各阶段成绩人数以及比例
void Analysis(double Sub[][K],int num,int cnt);
//对姓名进行排序
void NameSort(int num,char Name[][M],double Sum[],double Id[]);
//Swap交换学号以及成绩
void Swap(double *a,double *b);
//SwapN交换姓名
void SwapN(char *a,char *b);
//Safetyinput防止输入除了数字以外的字符 以及防止输入负数（成绩不为负）
int Safetyinput(void);

int main()
{
    printf("Number:200110732\nSubject7\n\n");

    double Sub[N][K],Sub_c[N][K];//Sub二维数组记录每人每科的成绩Sub_c保留输入顺序
    double Sum[N];//Sum记录每人总分
    double Id[N],Id_c[N];//Id_c负责按输入顺序保留数据
    char Name[N][M],Name_c[N][M];//存储姓名
    int num,choice,cnt;//num：学生人数：cnt：科目数目

    choice=Menu();

    while(choice!=0)
    {
        switch(choice)
        {
        case 1:
            printf("input the number of the students \n");
            do
            {
                num=Safetyinput();//防止恶意输入数字以外的字符
                if(num>=N||num<1)
                {
                    printf("please a number less than 30 but more than 1\n");
                }
            }
            while(num>=N||num<1);//防止数组越界访问

            printf("input the number of subjects\n");
            do
            {
                cnt=Safetyinput();
                if(cnt>=K||cnt<1)
                {
                    printf("please a number less than 7 but more than 1\n");
                }
            }
            while(num>=N||num<1);//防止数组越界访问

            ReadScore(num,cnt,Id,Id_c,Sub,Sub_c,Name,Name_c,Sum);
            break;
        case 2:
            Print(Sub_c,Id_c,cnt,num,Name_c,choice,Sum);
            break;
        case 3:
            Average(Sub,num,cnt);
            break;
        case 4:
            Print(Sub_c,Id_c,cnt,num,Name_c,choice,Sum);
            break;
        case 5:
            Scoresort(Sum,Id,num,Descending,choice,Name);
            break;
        case 6:
            Scoresort(Sum,Id,num,Ascending,choice,Name);
            break;
        case 7:
            IDsort(Sum,Id,num,Ascending,Name,choice);
            break;
        case 8:
            NameSort(num,Name,Sum,Id);
            break;
        case 9:
            IDsort(Sum,Id,num,Ascending,Name,choice);
            break;
        case 10:
            Scoresort(Sum,Id,num,Descending,choice,Name);
            break;
        case 11:
            Analysis(Sub,num,cnt);
            break;
        default:
            printf("input error!\n");
        }
        printf("input your next choice\n");
        choice=Safetyinput();
    }

    return 0;
}




//Safetyinput防止输入负数以及除了数字以外的字符
int Safetyinput(void)
{
    int x;
    while(scanf("%d",&x)==0||x<0||x>100)
    {
        printf("input a positive number less than 101 please!\n");
        while(getchar()!='\n');
    }

    return x;
}


/**********************************
** 函数名:Menu
** 输 入: void
** 输 出: 菜单
** x 为 1, 表示... ** x 为 0, 表示...
** 功能描述:显示菜单
** 全局变量:无
** 调用模块:
** 作 者:孙赫辰
** 日 期:11.2
** 修 改:
** 日 期:
** 版本
************************************/
int Menu(void)
{
    int choice;
    printf("1.Input record\n"
           "2.List record\n"
           "3.Calculate total and average score of every course\n"
           "4.Calculate total and average score of every student\n"
           "5.Sort in descending order by total score of every student\n"
           "6.Sort in ascending order by total score of every student\n"
           "7.Sort in ascending order by StudentID\n"
           "8.Sort in dictionary order by name\n"
           "9.Search by StudentID\n"
           "10.Search by name\n"
           "11.Statistic analysis for every course\n"
           "0.Exit\n"
           "Please enter your choice\n");

    do
    {
        printf("input the choice(input 1 except 0 at first)\n");
        choice=Safetyinput();
    }
    while(choice!=1&&choice!=0); //确保在执行除0以外的其他选项时先执行1

    return choice;
}

/**********************************
** 函数名:ReadScore
** 输 入: int num,int cnt,double Id[],double Id_c[],double Sub[][K],double Sub_c[][K],char Name[][M],char Name_c[][M],double Sum[]
** double Sub[][]:存有成绩
** double Id[]:存有Id
** double Id_c[]:保留顺序
** double Sub_c[]:保留顺序
** int cnt:科目数
** int num:人数
** char Name:存入姓名
** char Name_c:保留顺序
** double Sum[]:计算个人总分
** 输 出:
** x 为 1, 表示... ** x 为 0, 表示...
** 功能描述:按成绩降序或者升序排序 进行输出成绩单或者搜索姓名的成绩排名
** 全局变量:无
** 调用模块:
** 作 者:孙赫辰
** 日 期:11.2
** 修 改:
** 日 期:
** 版本
************************************/
void ReadScore(int num,int cnt,double Id[],double Id_c[],double Sub[][K],double Sub_c[][K],char Name[][M],char Name_c[][M],double Sum[])
{
    int i,j;
    for(i=0; i<num; i++)
    {
        Sum[i]=0;
        printf("input the ID and Name \n");
        scanf("%lf %s",&Id[i],Name[i]);

        printf("input the score of the each subject of the student\n");
        for(j=0; j<cnt; j++)
        {
            Sub[i][j]=Safetyinput();
            Sub_c[i][j]=Sub[i][j];
            Sum[i]+=Sub[i][j];
        }
        Id_c[i]=Id[i];
        strcpy(Name_c[i],Name[i]);
    }

    return;
}


void Print(double Sub_c[][K],double Id_c[],int cnt,int num,char Name_c[][M],int choice,double Sum[])
{
    //判断并执行choice==2或choice==4
    int i,j;

    for(i=0; i<num; i++)
    {
        if(choice==2)
        {
            printf("ID:%.0f\t\tName:%s\t\tScore:",Id_c[i],Name_c[i]);
            for(j=0; j<cnt; j++)
            {
                printf("%.1f\t",Sub_c[i][j]);
            }
        }
        if(choice==4)
        {
            printf("ID:%.0f\t\tNsme:%s\t\t",Id_c[i],Name_c[i]);
        }
        printf("the sum is%.1f\t\tthe average is%.1f\n",Sum[i],Sum[i]/cnt);

    }


    return ;
}


//Average计算每门课程平均分以及总分
void Average(double Sub[][K],int num,int cnt)
{
    int i,j;
    for(j=0; j<cnt; j++)
    {
        double sum=0;
        for(i=0; i<num; i++)
        {
            sum+=Sub[i][j];
        }
        printf("subject<%d>\ttotal sum is %.1f\ttotal average is %.1f\n",j+1,sum,sum/cnt);

    }

    return;
}


int Ascending (double a,double b)
{
    return a>b;
}


int Descending(double a,double b)
{
    return a<b;
}



void Swap(double *a,double *b)
{
    double t= *a;
    *a=*b;
    *b=t;

    return ;
}

/**********************************
** 函数名:Scoresort
** 输 入: double Sum[],double Id[],int num,int (*p) (double,double),int choice,char Name[][M]
** double Sum[]:存有成绩
** double Id[]:存有Id
** int num:人数
** int (*p)(double,double):指向Ascending或者Descending
** int choice:选择选项 排序成绩后 搜索姓名还是直接输出成绩单
** 输 出: 成绩顺序或者搜索的学号的对应成绩
** x 为 1, 表示... ** x 为 0, 表示...
** 功能描述:按成绩降序或者升序排序 进行输出成绩单或者搜索姓名的成绩排名
** 全局变量:无
** 调用模块:
** 作 者:孙赫辰
** 日 期:11.2
** 修 改:
** 日 期:
** 版本
************************************/
void Scoresort(double Sum[],double Id[],int num,int (*p) (double,double),int choice,char Name[][M])
{
    //采用选择排序
    int j,i;
    for(i=0; i<num-1; i++) //做n-1趟即可
    {
        int target=i;//每回从未归位的第一个数开始
        for(j=i+1; j<=num-1; j++)
        {
            if( (*p)(Sum[i],Sum[j]) )
            {
                target=j;
            }
        }
        Swap(&Id[target],&Id[i]);
        Swap(&Sum[target],&Sum[i]);
        SwapN(Name[target],Name[i]);
    }

    if(choice==10) //10都锁姓名
    {
        char key[M];
        printf("input the Name\n");
        scanf("%s",key);
        int i,flag=0;
        for(i=0; i<num; i++)
        {
            if(!strcmp(Name[i],key))
            {
                flag=1;
                break;
            }
        }
        if(flag)
        {
            printf("%.0f\t%s:\tranking:%d,score is %.1f\n",Id[i],key,i+1,Sum[i]);
        }
        else
        {
            printf("not found\n");
        }
    }
    else
    {
        printf("ID\tName\tranking\tScore\n");
        for(i=0; i<num; i++)
        {
            printf("%.0f\t%s:\t%d\t%.1f\n",Id[i],Name[i],i+1,Sum[i]);
        }
    }

    return;

}



void SwapN(char *a,char *b)
{
    char t[M];

    strcpy(t,a);
    strcpy(a,b);
    strcpy(b,t);

    return;
}


/**********************************
** 函数名:IDsort【二分搜索】
** 输 入:  double Sum[],double Id[],int num,int (*p) (double,double),char Name[][M],int choice
** double Sum[]:存入成绩
** double Id[]：存有Id
** int num：人数
** int (*P)(double,double)
** char Name[][M],
** int choice:传入选项 判断是否进行搜索学号
** 输 出:
** 功能描述:1.按学号排序2.搜索学号 并输出名次，成绩
** 全局变量:
** 调用模块:
** 作 者:孙赫辰
** 日 期:11.2
** 修 改:
** 日 期:
** 版本
************************************/
void IDsort(double Sum[],double Id[],int num,int (*p) (double,double),char Name[][M],int choice)
{
    int j,i;
    for(i=0; i<num-1; i++)
    {
        int target=i;//每回从未归的第一个数开始
        for(j=i+1; j<num; j++)
        {
            if( (*p) (Id[target],Id[j]) )
            {
                target=j;
            }
        }
        Swap(&Id[target],&Id[i]);
        Swap(&Sum[target],&Sum[i]);
        SwapN(Name[target],Name[i]);
    }

    //二分搜索
    if(choice==9)
    {
        int right=num-1,left=0;
        int mid=(right+left)/2;
        int flag=0;
        double key;
        printf("input the ID\n");
        scanf("%lf",&key);
        while(left<=right)
        {
            if(key<Id[mid])
            {
                right=mid-1;
            }
            else if(key>Id[mid])
            {
                left=mid+1;
            }
            else
            {
                flag=1;
                break;
            }
            mid=(left+right)/2;
        }
        if(flag)//flag判读是否找到 并且记录位置
        {
            for(i=0; i<num; i++)
            {
                if(Sum[mid]<Sum[i])
                {
                    flag++;
                }
            }
            printf("ID\tName\tranking\tScore\n");
            printf("%.0f\t%s:\t%d\t%.1f\n",Id[mid],Name[mid],flag,Sum[mid]);
        }
        else
        {
            printf("not found\n");
        }
    }
    else
    {
        printf("ID\t\tName\t\tScore\n");
        for(i=0; i<num; i++)
        {
            printf("%.0f\t\t%s:\t\t%.1f\n",Id[i],Name[i],Sum[i]);
        }
    }
    return;
}

//根据姓名排序
void NameSort(int num,char Name[][M],double Sum[],double Id[])
{
    int i;
    for(i=0; i<num-1; i++)
    {
        int j=i+1;
        int target=i;
        for( ; j<num; j++)
        {
            if(strcmp(Name[j],Name[target])<0)
            {
                target=j;
            }
        }
        Swap(&Id[target],&Id[i]);
        Swap(&Sum[target],&Sum[i]);
        SwapN(Name[target],Name[i]);

    }
    for(i=0; i<num; i++)
    {
        printf("%.0lf\t%s:\t%.1lf\t\n",Id[i],Name[i],Sum[i]);
    }

    return ;
}


//计算各门课程各分数段人数
void Analysis(double Sub[][K],int num,int cnt)
{
    int Grade[K][5]= {0},i,j;//Grade记录每门课程每个阶段的人数
    char *str[]= {"优秀","良好","中等","及格","不及格"};//char*str【】储存字符指针 依次指向各个标准评价
    for(j=0; j<cnt; j++)
    {
        for(i=0; i<num; i++)
        {
            int s=(int)Sub[i][j]/10;
            switch(s)
            {
            case 10:
            case 9:
                Grade[j][0]++;
                break;
            case 8:
                Grade[j][1]++;
                break;
            case 7:
                Grade[j][2]++;
                break;
            case 6:
                Grade[j][3]++;
                break;
            case 5:
            case 4:
            case 3:
            case 2:
            case 1:
            case 0:
                Grade[j][4]++;
                break;
            default:
                printf("error\n");
            }
        }
    }
    for(i=0; i<cnt; i++)
    {
        printf("subject <%d>\n",i+1);
        for(j=0; j<5; j++)
        {
            printf("%s\t：%d人占比%.0f%%\n",*(str+j),Grade[i][j],1.0*Grade[i][j]/num*100);
        }

    }

    return ;
}
