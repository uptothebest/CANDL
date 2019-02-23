R 인계
================
Dohyun Kim
2019년 1월 31일

Vector 파일 형태로 저장하기 <br>
--------------------------------

1.  Histogram 그리기

``` r
hist(rnorm(1000,0,1))
abline(v=0.7)
```

![](a_files/figure-markdown_github/cars-1.png)

<br> <br>

1.  저장하기 <br> <br> 2-1. plot창에서 저장 <br> <img src="https://user-images.githubusercontent.com/21265040/52025775-faf8e780-2548-11e9-91ba-bf52ee4045f0.png"> <br> <br> 2-2 command line으로 저장 <br>

``` r
### 여러 개의 파일을 저장해야할 필요가 있을 때는 코드가 유용함. 
win.metafile(filename = 'histogram.emf') ### 파일명을 먼저 설정
hist(rnorm(1000,0,1))
abline(v=0.7)  ## 그림 코드
dev.off()  ## 닫기를 해야 저장됨 
```

    ## png 
    ##   2

``` r
getwd()  ### 저장된 위치 확인 
```

    ## [1] "F:/Users/Kimdohyun/Dropbox/Dropbox/kaist/lab/presentation/Alumnus_workshop"

<br><br><br> \#\# Confidential interval이 있는 figure 그리기

mean (or median) 과 sd (or S.E) 값이 있어서 <br> 아래와 같은 데이터 프레임이 있다고 가정하면 <br>

    ##      feature mean_val sd_val
    ## 1      apple      2.5    1.3
    ## 2      grape      1.5    1.0
    ## 3     banana     -3.5    1.4
    ## 4 grapefruit      4.0    1.8
    ## 5       pear      2.0    1.2

<br><br><br>

기본 그림

``` r
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 3.4.4

``` r
ggplot(df, aes(x=feature, y=mean_val)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=0.1 ) ## Width 가 errorbar의 폭 
```

![](a_files/figure-markdown_github/unnamed-chunk-2-1.png)

``` r
ggplot(df, aes(x=feature, y=mean_val)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=1 ) ## Width 가 errorbar의 폭 
```

![](a_files/figure-markdown_github/unnamed-chunk-2-2.png)

<br><br><br>

벡터 파일 형태로 저장해서 PPT에서 바꾸면 편하지만 아래와같이 그림이 깨지는 경우가 있다. <img src="https://user-images.githubusercontent.com/21265040/52025773-fa605100-2548-11e9-9513-5c5916054a33.png">

<br><br> 유의한 것만 색깔을 칠하기 위해서는 조건을 추가해야한다.<br>

``` r
#nonsignicant, 양수, 음수를 표시하기 위해 새로운 factor를 만든다.
#feature 수가 5개이고, 조건은 3개 이므로 (Nonsignificant,양수,음수)
 df=cbind(df,as.factor(c(rep("P",3),"M","N"))) # plus, minus, nonsignficant 3,1,1, 이므로 5개가 된다.
colnames(df)[4]="Sign"  ## Column 이름 설정 
df
```

    ##      feature mean_val sd_val Sign
    ## 1      apple      2.5    1.3    P
    ## 2      grape      1.5    1.0    P
    ## 3     banana     -3.5    1.4    P
    ## 4 grapefruit      4.0    1.8    M
    ## 5       pear      2.0    1.2    N

``` r
NN=which(df$mean_val<0 & (df$mean_val+2*df$sd_val)<0) ## 유의하게 음수인 feature의 번호 획득 
PP=which(df$mean_val>0 & (df$mean_val-2*df$sd_val)>0) ## 유의하게 양수인 feature의 번호 획득 
NN
```

    ## [1] 3

``` r
PP
```

    ## [1] 4

``` r
df$Sign[NN]="M"
df$Sign[PP]="P"
df$Sign[-c(PP,NN)]="N"
## 유의성 맞게 Sign column의 값을 변경
df
```

    ##      feature mean_val sd_val Sign
    ## 1      apple      2.5    1.3    N
    ## 2      grape      1.5    1.0    N
    ## 3     banana     -3.5    1.4    M
    ## 4 grapefruit      4.0    1.8    P
    ## 5       pear      2.0    1.2    N

``` r
ggplot(df, aes(x=feature, y=mean_val,color=Sign)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=0.1 ) +
 scale_color_manual(values=c('blue','gray','red')) ## 색을 추가 
```

![](a_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
ggplot(df, aes(x=feature, y=mean_val,color=Sign)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=0.1 ) +
 scale_color_manual(values=c('blue','gray','red')) +geom_hline(yintercept=0, col="black", linetype="dashed") ## 수평선 추가
```

![](a_files/figure-markdown_github/unnamed-chunk-3-2.png)

``` r
ggplot(df, aes(x=feature, y=mean_val,color=Sign)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=0.1 ) +
 scale_color_manual(values=c('blue','gray','red')) +geom_hline(yintercept=0, col="black", linetype="dashed") +
  theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),panel.background = element_blank())+
  theme(legend.position='None', legend.title = element_blank()) ## 배경과 figure legend 지움 
```

![](a_files/figure-markdown_github/unnamed-chunk-3-3.png)

``` r
ggplot(df, aes(x=feature, y=mean_val,color=Sign)) + 
  geom_errorbar(aes(ymin=mean_val-2*sd_val, ymax=mean_val+2*sd_val), width=0.1 ) +
 scale_color_manual(values=c('blue','gray','red')) +geom_hline(yintercept=0, col="black", linetype="dashed") +
  theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),panel.background = element_blank())+
  theme(legend.position='None', legend.title = element_blank())+
 theme(axis.text.x = element_text(angle = 45, vjust = 1, size = 13, hjust = 1),
        axis.text.y = element_text(size=13)) ## 글자 기울이기 폰트 변경 
```

![](a_files/figure-markdown_github/unnamed-chunk-3-4.png)

<br><br> 변수가 적을 때는 상관없지만 변수가 많으면 기울이기 안하면 글자가 겹친다.<br> <img src="https://user-images.githubusercontent.com/21265040/52025774-fa605100-2548-11e9-97ef-8a896ef0049a.png">
