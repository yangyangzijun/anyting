#### 78
给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。
#### 解法1
```
void sa(vector<int>& nums,vector<int>& temp,vector<vector<int>> &res,int n)
{
    if(temp.size()<=nums.size())  {res.push_back(temp);
       }
   for(int i=n;i<nums.size();i++)
   {
       temp.push_back(nums[i]);
       sa(nums,temp,res,i+1);
       temp.pop_back();
   }


}
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    if(nums.size()==0) return  res;
    vector<int> temp;
    sa(nums,temp,res,0);
    return  res;
}
```
##### 解法2 
```
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res(1);
    for(int i=0;i<nums.size();i++)
    {
        int length=res.size();
        for(int j=0;j<length;j++)
        {
            vector<int> k=res[j];
            k.push_back(nums[i]);
            res.push_back(k);
        }
    }


    return  res;
}

```
