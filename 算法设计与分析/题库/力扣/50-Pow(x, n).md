# [Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

![image-20210604110211027](assets/50-Pow(x,%20n)/image-20210604110211027.png)

```
    public double pow(double x,int n){
        if(n == 0) return 1;
        else{
            double num = pow(x,n/2);
            if(n % 2 != 0) return num*num*x;
            else return num*num;
        }
    }

    public double myPow(double x, int n) {
        if(n<0){
            x = 1/x;
            n = Math.abs(n);
        }
//        System.out.println(pow(x,n));
        return pow(x,n);
    }
```

