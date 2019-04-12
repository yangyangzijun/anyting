#include <iostream>
#include <vector>
#include <algorithm>
using  namespace std;
void BubblSort(vector<int> &list)//起泡排序 每一趟排序使的前面的排序列+1
{
    int l_len=list.size();
    for(int i=1;i<l_len;i++)
    {
        for(int j=l_len-1;j>=i;j--)
        {
            if(list[j-1]>list[j]) swap(list[j-1],list[j]);
        }
    }
}
void BubblSort_1(vector<int> &list)//起泡排序的改进
{
    int l_len=list.size();
    for(int i=1;i<l_len;i++)
    {
        bool flag=false;//逆序对标志  如无逆序对 排序成功
        for(int j=l_len-1;j>=i;j--)
        {
            if(list[j-1]>list[j])
            {
                swap(list[j-1],list[j]);
                flag= true;
            }
        }
        if(flag== false)
        {
            return;
        }
    }
}
void InsertSort(vector<int> &list)//插入排序 O(n^2)
{
    int l_len=list.size();
    for(int i=1;i<l_len;i++)
    {
        if(list[i-1]>list[i])
        {
            int temp=list[i],j=i-1;
            do{
                list[j+1]=list[j];
                j--;
            }while (j>=0&&temp<list[j]);
            list[j+1]=temp;
        }
    }
}
void InsertSort_1(vector<int> &list)//折半插入排序 O(n*long n)
{
    int l_len=list.size();
    for(int i=1;i<l_len;i++)
    {
        if(list[i-1]>list[i])
        {
            int temp=list[i],l=0,r=i-1;
            while (l<=r)
            {
                int mid=(l+r)/2;
                if(list[mid]<=temp)
                {
                    l=mid+1;
                } else
                {
                    r=mid-1;
                }
            }
            for(int j=i-1;j>=l;j--)
            {
                list[j+1]=list[j];
            }
            list[l]=temp;

        }
    }
}
int  median3(vector<int> &list,int left,int right)
{
    int mid=(left+right)/2;
    int k=left;
    if(list[mid]<list[k]) k=mid;
    if(list[right]<list[k]) k=mid;
    if(k!=left) swap(list[k],list[left]);
    if(mid!=right&&list[mid]<list[right]) swap(list[mid],list[right]);
    return list[right];
}
int parttition(vector<int> &list,int  left,int right)
{
   if(left<right)
   {
       int l=left,r=right-1, povit=median3(list,left,right);
       while (true)
       {
           while (l<r&&list[l]<povit) l++;
           while (l<r&&list[l]>=povit) r--;
           if(l<r) {
               swap(list[l],list[r]);
               l++;r--;
           } else
               break;
       }
       if(list[l]>povit)
       {
           list[right]=list[l];
           list[l]=povit;
       }
       return  l;
   }

}
void mege(vector<int> &T1,vector<int> &T2,int left,int mid,int right)
{
for(int i=left;i<=right;i++)
{
    T2[i]=T1[i];
}
int s_1=left,s_2=mid+1,t=left;
while (s_1<=mid&&s_2<=right)
{
    if(T2[s_1]<= T2[s_2]) T1[t++]=T2[s_1++];
    else T1[t++]=T2[s_2++];
}
while (s_1<=mid) T1[t++]=T2[s_1++];
while (s_2<=right) T1[t++]=T2[s_2++];
}
void megeSort(vector<int> &T1,vector<int> &T2,int left,int right)
{
    if(left>=right) return;
    int mid=(left+right)/2;
    megeSort(T1,T2,left,mid);
    megeSort(T1,T2,mid+1,right);
    mege(T1,T2,left,mid,right);
}
void quiksort(vector<int> &list,int  left,int right)
{
    if(left<right)
    {
        int location=parttition(list,left,right);
        quiksort(list,left,location-1);
        quiksort(list,location+1,right);
    }
}
int main()
{
    vector<int> list={3,1,2,565,84,1245,75};
    vector<int> temp(100);
    megeSort(list,temp,0,list.size()-1);
    for(int l:list)
    {
        cout<<l<<" ";
    }
}
