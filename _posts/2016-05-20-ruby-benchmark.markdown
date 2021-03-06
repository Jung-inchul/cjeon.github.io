---
layout: post
title: 루비 벤치마크 하기
tags: ruby, benchmark, test, 루비, 벤치마크, 테스트
---
실제로 돌아가는 코드를 짜다보면, 코드의 성능을 단순히 elegance나 O(n)으로만 설명하기는 힘듭니다. 가장 절대적인 기준은 아마도 코드가 **실제로 돌아가는 시간** 과 **실제로 사용하는 메모리 용량** 일텐데요. 보통은 메모리 사용량보다 시간이 얼마나 걸리는 지가 중요하게 생각됩니다. 오늘 글에서는 **Ruby에서 벤치마크 하기** 를 간단하게 다뤄보겠습니다.

## 1. 가장 간단한 벤치마크
아마 가장 간단한 벤치마크는 `Time.now`를 이용한 벤치마크 일겁니다. 저도 간단한 벤치마크를 할 때는 이 방법을 주로 사용합니다.

``` ruby
start_time = Time.now   
(do something here)   
end_time = Time.now   
time_taken = end_time - start_time   
```

이렇게 하면 `time_taken`이 걸린 시간이 되죠. 하지만 이 방법은 테스트 대상 함수가 조금만 복잡해져도 사용하기 번거롭고, 또 어디서 시간이 많이 소요되는 지 찾으려면 코드가 아주 난잡해집니다.  

이런 문제를 해결하기 위해 [Ruby Benchmark Module](http://ruby-doc.org/stdlib-2.0.0/libdoc/benchmark/rdoc/Benchmark.html)이 존재합니다.

## 2. 벤치마크 모듈 사용하기
벤치마크 모듈을 이용해서 위 예시를 아래와 같이 구현할 수 있습니다.

``` ruby  
require 'benchmark'  
time = Benchmark.measure do  
  (do something here)  
end  
```
그러면 아래와 같은 형태의 값을 얻을 수 있는데요,

```
1.166667   0.050000   1.216667 (  0.571355)
```
각각  
1. **User CPU time: 실제 코드를 실행하는 데 걸린 시간,**  
2. **System CPU time: 커널 코드를 실행하는 데 걸린 시간,**   
3. **User CPU time + System CPU time,**  
4. **Elapsed real time (실제로 경과한 시간)을 뜻합니다.**  

또, 여러 코드를 한 번에 벤치마크 하기도 쉽습니다. 아래 예시를 보시죠.

``` ruby
require 'benchmark'  

n = 50000  
Benchmark.bm(7) do |x|  
  x.report("for:")   { for i in 1..n; a = "1"; end }  
  x.report("times:") { n.times do   ; a = "1"; end }  
  x.report("upto:")  { 1.upto(n) do ; a = "1"; end }  
end  
```
이 코드를 실행하면 아래와 같은 결과를 얻을 수 있습니다.

```
user     system      total        real  
for:     1.050000   0.000000   1.050000 (  0.503462)  
times:   1.533333   0.016667   1.550000 (  0.735473)  
upto:    1.500000   0.016667   1.516667 (  0.711239)  
```
이외에도 벤치마크를 두 번 돌려서 garbage collection 등이 코드에 주는 영향을 최소화하는 `bmbm`이라는 method도 있으니 참고하시기 바랍니다.

## 3. 더 읽을만한 글  

1. <a>http://www.skorks.com/2010/03/timing-ruby-code-it-is-easy-with-benchmark/</a>  
2. <a>http://stackoverflow.com/questions/1616269/ruby-benchmark-module-meanings-of-user-system-and-real</a>  
3. <a>https://gist.github.com/anildigital/1229896</a>
4. <a>http://greyblake.com/blog/2012/09/02/ruby-perfomance-tricks/</a>    
