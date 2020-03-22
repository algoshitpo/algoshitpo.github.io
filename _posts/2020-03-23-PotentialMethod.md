---
layout: post
title: 'Potential Method'
author: ryute
date: 2020-03-23 02:00
tags: [Math]
---

$$
\newcommand{\PhiInsert}{\Phi_{insert}}
\newcommand{\PhiDelete}{\Phi_{delete}}
\newcommand{\size}{\mbox{size}}
\newcommand{\capa}{\mbox{capacity}}
\newcommand{\di}{D_{i}}
\newcommand{\dim}{D_{i-1}}
\newcommand{\zig}{\mbox{Zig}}
\newcommand{\zzig}{\mbox{Zig-zig}}
\newcommand{\zzag}{\mbox{Zig-zag}}
\newcommand{\bf}{\mbox{Before}}
\newcommand{\aft}{\mbox{After}}
\newcommand{\subt}{\mbox{Subtrees}}
$$

## Intro

Potential Method가 뭘까요? Potential Method는 우리가 흔히 사용하는 Amortized 분석과 큰 연관성을 가지고 있습니다. 어떠한 자료구조의 상태가 있을 때, 그 자료구조가 잘 작동하기 위한 '이상적인' 상태가 존재합니다. 하지만 일반적으로 이 이상적인 상태를 유지하는 것은 그렇게 쉽지 않습니다. 항상 자료구조를 이상적인 상태로 유지하는 것은 굉장히 많은 비용을 요구합니다. 생각해 볼 수 있는 대안은, 자료구조를 매번 갱신하는 것이 아니라 적당히 비효율적인 연산을 하다가, 한 번에 효율적인 상태로 자료구조를 갱신해주는 것입니다. 이 둘이 균형을 이룰수록 전체적인 시간복잡도는 낮아지게 됩니다. 이 때 비효율적인 연산과 효율적인 상태로 자료구조를 갱신하는 것을 합쳐 평균적으로 특정 복잡도 이하가 됨을 보장할 수 있다면, 이를 일반적으로 Amortized 복잡도라고 합니다.

대표적인 Amortized 복잡도의 예로는 Array Doubling 기법이 있습니다. Array Doubling 기법은 동적 배열을 할당할 때 현재 배열 크기의 두 배씩 할당하고, 줄일 때는 4분의 3 이상 비어 있어야 크기를 절반으로 줄이는 것입니다. 어떤 배열에 원소를 집어넣을 때 만약 배열이 꽉 찬다면 그 배열을 전부 새로운 연속된 메모리를 할당하여 이동해야 합니다. 하지만 이 때 메모리를 추가로 얼마정도 잡아줘야 효율적으로 배열을 확장할 수 있을까요? 앞에서 언급했듯이 자신의 크기만큼 잡아주는게 최적입니다. 배열의 크기가 $N$일 때 새로 추가되는 공간의 크기는 $N$이고, 최대 $N$개의 원소 추가 쿼리를 $O(1)$에 수용할 수 있으며, 한 번 공간을 추가할 때 시간은 $O(N)$만큼 듭니다. 따라서 결과적으로 한 개의 원소를 추가할 때, 평균적으로는 $O(1)$ 시간에 쿼리를 처리할 수 있습니다. 지울 때도 마찬가지입니다.

이러한 연산을 진행할 때, 자료구조가 얼마나 '망가져' 있는지를 나타내는 지표를 Potential이라고 합니다. 자료구조가 에너지를 가지고 있다고 생각하고 현실처럼 에너지가 낮을 때 안정하다고 합시다. 자료구조가 Potential이 높은 상태라면, 안정하지 못한 상태입니다. 우리는 여기에 특정한 연산을 적용해서 Potential을 낮출 수 있습니다. Potential이 높으면, 연산을 적용하는 데 일반적으로 시간이 더 많이 걸립니다. 이 Potential을 적당히 유지하면서 자료구조를 관리하기 위해서는 Potential이라는 개념을 정량적으로 관리할 수 있어야 합니다.



## Potential의 수학적 정의

함수 $ \Phi $ 를 자료구조의 상태, 즉 포텐셜을 나타내기 위한 함수로 정의할 수 있습니다. 이 함수는 자료구조의 상태를 음이 아닌 정수로 변환해줍니다. $D$가 자료구조의 상태라고 하면, $ \Phi (D) $는 이 자료구조의 현재 Potential Energy를 나타냅니다. 다른 말로는, 자료구조가 얼마나 '이상적인 상태'에서 먼지를 나타내기도 합니다. $\Phi$는 자유롭게 잡을 수 있지만, $\Phi(D_0) = 0$이여야 하며 $\Phi (\di) \ge 0$ 이어야 합니다. 이 조건을 만족하는 선에서 후술할 amortized time이 최대한 균일하게 유지될 수 있도록 함수를 적당히 설정해 주면 됩니다.

$o$를 자료구조에서 수행하는 연산 중 하나라고 합시다. $_{before}$을 연산 $o$를 수행하기 전 자료구조의 상태, $_{after}$을 연산 $o$를 수행한 이후의 자료구조의 상태라고 할 때, $ \Phi $에 대해 연산 $o$의 amortized time은 다음과 같습니다.
$$
T_{amortized}(o) = T_{actual}(o)+C \cdot (\Phi(D_{after})-\Phi(D_{before}))
$$
다시 말해, 연산 $o$의 amortized time은 단순하게 연산 $o$를 수행하는 시간 뿐만 아니라, 이 연산을 통해서 앞으로의 자료구조가 얼마나 더 효율적으로/비효율적으로 변하는지까지 반영하고 있습니다. 상수 $C$는 음이 아닌 정수로 상태 간의 potential과 실제로 수행해야 하는 연산에 대한 비례 상수를 나타냅니다만, 일반적으로 Big O Notation에서는 상수를 생략하므로 보통 $C$ 또한 생략됩니다.

Potential을 사용해서 amortized time을 다음과 같이 적용할 수 있다는 것은 알았습니다. 그렇다면 이 amortized time을 실제 문제에는 어떻게 적용할 수 있을까요?  다음과 같이 생각해 봅시다. 자료구조에 대해 연산 순열 $O = o_1, o_2, \cdots ,o_n$을 적용한다고 할 때 총 amortized time은 $ \displaystyle T_{amortized}(O) = \sum_{i=0}^{n} T_{amortized}(o_i)$ 입니다. 그리고 실제 수행 시간은 $ \displaystyle T_{actual}(O) = \sum_{i=0}^{n} T_{actual}(o_i)$ 가 됩니다.여기서 $T_{amortized}(o)$를 위에 서술한 정의대로 풀어 보면 다음과 같게 됩니다.
$$
\displaystyle T_{amortized}(O) = \sum _{i=1} ^{n} (T_{actual}(o_i) + C \cdot (\Phi (S_i) - \Phi(S_{i-1}))) = T_{actual}(O) + C \cdot (\Phi (S_n) - \Phi(S_0))
$$
$D_i$를 $i$번 연산을 수행하고 나서의 자료구조의 상태라고 하면, 식에서 $D_i$가 모두 상쇄되어 사라지면서 처음 항과 마지막 항만이 남게 됩니다. 이때 $\Phi (D_0) = 0$ 이고 $\Phi (D_n) \ge 0$이어서  언제나 $T_{actual}(O) \le T_{amortized}(O)$ 이 됩니다. 따라서 $T_{amortized}(O)$는 $T_{actual}(O)$보다 $C \cdot \Phi (D_n)$ 만큼만 큰, 상당히 정확한 상계로 활용할 수 있습니다. 시간복잡도를 측정하는 유용한 지표가 되는 것이죠.



## Dynamic Array Problem

위에 설명했던 동적 배열의 할당을 예시로 들어서 이 개념을 이해해봅시다. Potential 함수를 정의하기 전에 먼저 Dynamic Array가 어떻게 동작하는 지 다시 한 번 정확하게 짚고 넘어가는 것이 좋겠군요.

* 만약 배열이 모두 찼다면 현재 배열 크기의 두 배의 크기를 가진 새로운 배열에 복사해 넣습니다.
* 만약 배열이 4분의 1 이하로 차 있다면, 현재 배열 크기 절반의 크기를 가진 새로운 배열에 복사해 넣습니다.

우리가 증명하고자 하는 것은 다음과 같습니다.

* 비어 있는 Dynamic Array에 임의의 순서로 $n$번의 삽입과 삭제 연산을 수행했을 때, $O(n)$ 시간만이 걸립니다.



#### Incremental Dynamic Array

먼저 삽입 연산만이 존재한다고 가정합시다. 그러면 우리가 증명하고자 하는 명제는 

- 비어 있는 Dynamic Array에 $n$번의 삽입 연산을 수행했을 때, $O(n)$ 시간만이 걸립니다.

가 됩니다. 이를 증명하기 위해서 적절한 Potential Function을 정의합시다.
$$
\Phi _{insert} (D_i) = 2 \cdot  \mbox{size} (D_i) - \mbox {capacity}(D_i)
$$
이때 $\PhiInsert(D_0) = 0$이고, 원소의 개수의 2배보다 배열의 크기가 클 수 없기 때문에 모든 $i$에 대해서 $\PhiInsert(D_i) \ge 0$ 입니다. 이제 원소가 추가될 때 배열의 Potential이 실제로 어떻게 변하는지 확인해봅시다.



1. 배열이 확장되지 않는 경우

이 경우 $\capa(D_i)$ = $\capa (D_{i-1})$입니다. 연산을 수행하는 데 요구되는 실제 비용 $T_{actual}(o_i)=1$이고, 
$$
\PhiInsert(D_i) - \PhiInsert(D_{i-1}) = (2 \cdot \size (D_i) - \capa (D_i)) - (2 \cdot \size(D_{i-1}) - \capa (D_{i-1})) = 2
$$
입니다.  따라서 amortized time은 $T_{actual}(o_i) + (\PhiInsert(D_i)-\PhiInsert(D_{i-1})) = 3$ 입니다.



2. 배열이 확장되는 경우

이 경우 $\capa (D_i) = 2 \cdot \capa (D_{i-1})$ 입니다. 현재 배열에 있는 원소들을 조금 더 넓은 여유 공간이 있는 곳으로 옮겨줘야 하고 새로운 원소 하나를 추가해야 하므로, 연산을 수행하는 데 요구하는 실제 비용인 $T_{actual} (o_i) = 1 + \capa(D_i)$ 입니다. 하지만 이렇게 한 연산의 비용이 늘어나는 만큼 요구되는 Potential의 양은 줄어듭니다. 보다 구체적으로,
$$
\begin{eqnarray*}
\PhiInsert (D_i) - \PhiInsert(D_i-1) &=& (2 \cdot \size (D_i) - \capa (D_{i})) - (2 \cdot \size(\dim) - \capa (\dim)) \\
&=& 2-\capa (D_i) + \capa (D_{i-1}) \\
&=& 2-\capa (D_{i-1})

\end{eqnarray*}
$$
가 됩니다. 이 경우에도 amortized time은 위 둘의 합으로
$$
\begin{eqnarray*}
T_{actual}(o_i) + (\PhiInsert(D_i) - \PhiInsert(D_{i-1})) &=& 1 + \capa(D_{i-1}) + (2-\capa(D_{i-1})) \\
&=& 3

\end{eqnarray*}
$$
이 됩니다.

각 연산의 amortized time이 $3$으로 동일하므로, $n$번의 연산 이후에도 amortized time이 실제 수행 시간보다 항상 크다는 증명에 따라 실제 연산의 수행 횟수가 $3n$번을 초과하지 않습니다. 따라서 Big O notation으로, 각 연산의 수행 시간은 $O(1)$이 됩니다.



#### Fully Dynamic Array



이제 $\PhiInsert$가 아닌 삽입, 삭제가 모두 가능하다는 가정 하에서의 Potential Function $\Phi$를 정의해봅시다. 증명해야 하는 Lenma는 다음과 같고, 이를 위해 Potential Function을 다음과 같이 정의할 수 있습니다.

* 비어 있는 Dynamic Array에 $n$번의 삽입 또는 삭제 연산을 임의의 순서로 수행했을 때, $O(n)$ 시간만이 걸립니다.

$$
\Phi(D_i) = \left\{ \begin{array}{ll}
         2 \cdot \size(\di) - \capa(\di) &
         \mbox{if} \ \ 2 \cdot \size (\di) \ge \capa(\di) \\
        \capa(\di)/2 -\size(\di) &
        \mbox{if} \ \  \capa(\di)  > 2 \cdot \size(\di)
        \end{array} \right.
$$

이 함수는 Potential Function의 성질인 $\Phi (D_0)=0$, 그리고 $\Phi(\di) \ge 0$을 자명하게 만족합니다. 이제 실제 연산에서의 amortized time을 분석해 봅시다. 

먼저 원소를 삽입할 때는 위에 서술한 증명과 같이 potential time이 $3$이라는 것을 알 수 있습니다. 그렇다면 원소를 삭제할 때는 어떨까요? 이 경우 배열의 크기가 절반으로 줄어들게 되므로 $ \displaystyle \capa(\di) = \frac{1}{2} \capa(\dim)$입니다. 연산을 수행하는 데 필요한 비용은 배열 전체를 옮겨야 하므로 $\displaystyle T_{actual}(o) = \size (\di) = \frac{1}{4} \capa(\di)$입니다. 역시 이 때도 퍼텐셜이 달라집니다. 보다 구체적으로,
$$
\begin{eqnarray*}
\Phi (D_i) - \Phi(D_i-1) &=& (\capa(\di)/2 - \size (\di)) - (\capa(\dim)/2 - \size (\dim)) \\
&=& 1 + \capa(\di)/2 -\capa(\dim)/2 \\
&=& 1-\capa (D_{i}) / 4

\end{eqnarray*}
$$
와 같습니다. 이 경우에 potential time을 분석해 보면 
$$
\begin{eqnarray*}
T_{actual}(o_i) + (\Phi(D_i) - \Phi(D_{i-1})) &=& \capa(D_{i}) / 4 + (1-\capa(D_{i})/4) \\
&=& 1

\end{eqnarray*}
$$
입니다.  결과적으로 삽입과 삭제에서 모두 amortized time이 $O(1)$이므로, Dynamic Array에서의 삽입과 삭제는 실제로도 amortized $O(1)$에 수행된다는 결론을 낼 수 있습니다.



## Splay Tree

Splay Tree는 많은 문제에서 활용되는 이진 탐색 트리입니다. Splay Tree의 재밌는 점은 여타 다른 자가 균형 이진 탐색 트리와는 달리 항상 균형잡힌 꼴을 유지하고 있지 않다는 것입니다. 그럼에도 불구하고 Splay Tree가 쓰일 수 있는 이유는 Splay Tree는 고유의 Splay 연산을 통해서 기초 연산을 트리의 크기 $N$에 대해 amortized $O(\lg N)$ 시간에 수행할 수 있기 때문입니다. 이를 직관적으로 이해하기는 매우 쉽지 않습니다만, Potential Method를 통해서 이를 증명할 수 있습니다. 이 문단에서 보이고자 하는 바는, 다음 두 정리가 Splay Tree에서 성립하는 것을 보이는 것입니다.

1. Splay Tree에서의 Splay 연산은 amortized $O(\lg N)$ 시간에 수행 가능하다.

2. Splay Tree에서의 삽입, 삭제, 검색 연산은 amortized $O(\lg N)$ 시간에 수행 가능하다.



이 문단은 독자가 Splay Tree의 기본적인 작동 원리를 모두 안다고 가정하고 설명을 진행합니다. 일단 설명을 시작하기 전에 Potential Function을 정의하기 위한 몇 가지 표기법들을 정합니다. 

* 이진 탐색 트리 $T$에 대하여 $S(x)$는 $x$를 루트로 하는 서브트리에 있는 정점 개수입니다.
* 이진 탐색 트리 $T$에 대하여 $x$의 rank인 $r(x)$는 $ \lfloor \lg S(x) \rfloor $로 정의됩니다.

그 이후 Splay Tree $T$에 대한 Potential Function $\Phi (T)$를 정의합시다.

* Splay Tree $T$에 대하여 Potential Function $\displaystyle \Phi(T) = \sum_{x \in T} r(x) $ 입니다.



### Splay 연산

Splay Tree의 시간복잡도를 보장해 주는 일등공신, Splay 연산의 시간복잡도부터 분석해야 나머지 작업이 쉬워집니다. 시간복잡도를 분석하기 전에 자료구조의 연산에 Dynamic Array에서 써먹었던 개념인 amortized time을 적용하는 것을, 앞으로는 amortized cost의 약자인 $AC(o)$로 줄여 쓰도록 하겠습니다. 우리가 처음 참임을 보일 정리는, 

* $AC(Splay(x)) \le 3(r(root)-r(x))+1$ 이다.

입니다. 

이 정리를 증명하기 위해서 간단한 보조정리들부터 시작합시다.

**Lemma 1.** 정점 $x$가 두 자식 정점 $a$와 $b$를 가지고 $r(a)=r(b)=r$이라면, $r(x) > r$ 이다.

Proof.  $S(x) = S(a)+S(b) + 1> S(a) + S(b) \ge 2^{r(a)} + 2^{r(b)} = 2^r + 2^r =2^{r+1}$이므로, $r(x) \ge r+1$이다. 

**Corollary 2.** 정점 $x$가 두 자식 정점 $a$와 $b$를 가지고 $r(x) = r(a)$라면, $r(x) > r(b)$이다.

**Corollary 3.** 정점 $x$가 두 자식 정점 $a$와 $b$를 가지고 $r(x) = r(a)$라면, $2r(x) > r(a) +r(b)$ 이다.



이제 이 당연한 사실들만 가지고 처음 정리를 증명하려고 시도해봅시다. Splay 연산은 Zig 스텝과 Zig-zig, Zig-zag 스텝으로 구성되어 있으므로, 이 스텝들을 각각 증명하면 도움이 크게 될 것 같습니다. $C$를 자식 정점, $P$를 부모 정점, $GP$를 부모 정점의 부모 정점이라고 할 때 Zig 스텝과 Zig-zig, Zig-zag 스텝에 대해서

**Lemma 4.** $AC(\zig) \le 3(r(root)-r(C)) +1 $ 이다.

**Lemma 5.** $AC(\zzig), AC(\zzag) \le 3(r(GP)-r(C))$ 이다.

를 따로따로 증명해봅시다. Splay 연산에서 하나의 step을 진행할 때는 부모 정점이 루트이면 Zig, 그렇지 않다면 Zig-zig 또는 Zig-zag 연산을 반복적으로 수행하게 되므로, 이 연산 각각에 대한 amortized cost를 계산한 후 이 연산들을 연속해서 수행했을 때의 시간복잡도를 증명하면 됩니다.



[이미지 1], [이미지 2]

먼저 부모가 루트 정점일 때 수행되는 시간복잡도를 나타내는 **Lemma 4**입니다. Zig 스텝에 의해서 왼쪽 트리가 오른쪽과 같이 바뀐다고 하면 서브트리 $A$, $B$, $C$는 그대로이고 $x$와 $p$의 위치만 바뀌게 됩니다. 연산을 수행하고 나서의 $x$와 $p$를 각각 $x'$와 $p'$이라고 하면, 트리 $T$와 연산 수행 후 바뀐 트리 $T'$의 포텐셜은 다음과 같이 변화하게 됩니다.
$$
\begin{eqnarray*}
\Phi(T) &=& r(x) + r(p) + \Phi(A) + \Phi(B) + \Phi(C) \\ \Phi(T') &=& r(x') + r(p') + \Phi(A) + \Phi(B) + \Phi(C) \\
\end{eqnarray*}
$$
$r(p) = r(x')$ 이므로, $ \Delta \Phi = \Phi(T') - \Phi(T) = r(p') - r(x)$ 입니다. $AC(\zig)$는 정의에 따라 Potential의 변화에 실제로 Rotation을 수행하는 데 드는 단위 연산량 $1$을 더해 $1+\Delta \Phi = 1 + r(p') - r(x)$일 텐데, 
$$
\begin{eqnarray*}
&AC(\zig)& &=&1+r(p')-r(x) \\
&&&\le& 1 + r(p)- r(x) & \mbox{since} \ \ r(p) \ge r(p') \\
&&&\le& 3(r(p)-r(x))+1
\end{eqnarray*}
$$
입니다. 여기서 우리가 수행한 $x$의 부모 정점은 루트이니 $p=root$이고, $x=C$가 되므로 **Lemma 4**의 증명이 끝났습니다. 왜 굳이 3을 곱해서 부등식을 비효율적으로 만드냐고 생각할 수도 있지만, 이는 **Lemma 5**를 증명하면 알 수 있습니다.



부모가 루트 정점이 아닐 때 수행되는 시간복잡도를 나타내는 **Lemma 5**입니다. 이 경우에는 Zig-zig나 Zig-zag 연산을 수행하면서 $GP$까지 동원하게 되는데요, 편의를 위해 연산 전 정점들의 rank를 $r=r(GP)$, $b=r(P)$, $a=r(C)$로 표현하도록 하겠습니다. $r\ge a$인 것은 자명하니 각각을 $r=a$인 경우와 $r>a$인 경우로 케이스를 나누어 생각하겠습니다.

1. $r>a$인 경우

$$
\Phi(\mbox{Before}) = r+b+a+\Phi(\mbox{Subtrees}) \ge r+2a+\Phi(\mbox{Subtrees}) \\
\Phi(\mbox{After}) \le 3r+ \Phi(\mbox{Subtrees})
$$

임이 $r \ge b \ge a$임에 따라 자명합니다. 따라서
$$
\Delta \le 3r-(r+2a)=2(r-a)
$$
이고, 실제 수행 시간 $1$을 더해서
$$
AC=1+\Delta \Phi \le 1+2(r-a) \le 3(r-a) = 3(r(GP)-r(c))
$$
입니다.



2. $r=a$인 경우

$r \le b \le a$이면서 $r=a$이므로, $r=a=b$ 입니다. 따라서
$$
\Phi (\bf) = r+b+a+\Phi(\subt) = 3r+\Phi(\subt)
$$
입니다. 이제 Lemma 5를 증명하려면 $\Phi (\aft) \le 3r-1+\Phi(\subt)$인 것만 보이면 됩니다.

만약 Zig-zag 연산을 수행한다면, 트리는 $C$를 루트로 하는 다음과 같은 구조로 변화하게 됩니다.

[이미지 3]

**Corollary 3**에 의해 $r(GP) + r(P) < 2(C)$임을 알 수 있습니다. 따라서
$$
\Phi(\aft) = r(C)+r(GP)+r(P) + \Phi(\subt) \le 3r(C)-1+\Phi(\subt) = 3r-1+\Phi(\subt)
$$
입니다. (rank는 모두 양의 정수이어서 성립합니다.)

만약 Zig-zig 연산을 수행한다면, 트리는 이렇게 두 단계에 걸쳐 변화하게 됩니다.

[이미지 4]

일단 $C$를 루트로 하는 서브트리들에는 전혀 변함이 없습니다. 따라서 모든 상황에 대해 $r(C)=r$임을 알 수 있습니다. 가운데 상황에서 $r(P)$또한 첫 번째 상황에서의 $GP$와 같은 상황에 놓여 있으므로 $r(P)=r$입니다. 역시 가운데 상황에서, $GP$는 **Corollary 2**에 의해 $r(P)=r(C)$이고 $P$의 두 자식 정점이 $C$와 $GP$ 이므로 $r(GP)<r$이 됩니다. 마지막 상황에서도 $GP$를 루트로 하는 서브트리들에는 변함이 없으므로, $r(GP)<r$이  유지됩니다. 이 사실들을 종합해 볼 때,
$$
\begin{eqnarray*}
\Phi(\aft) &=& r(C) + r(P) + r(GP) + \Phi(\subt) \\ &\le& r+r+(r-1) + \Phi(\subt) \\ 
&=& 3r-1+\Phi(\subt)
\end{eqnarray*}
$$
임을 알 수 있습니다.

결과적으로  $\Delta \Phi = \Phi(\aft) - \Phi(\bf) \le -1$이니,
$$
AC=1+\Delta \Phi \le 1+(-1) = 0 \le 3(r(GP)-r(C))
$$
이 됩니다. 

이로서 **Lemma 4**와 **Lemma 5**가 모두 참이라는 것을 알았습니다. 그렇다면 이를 이제 Splay 연산에 어떻게 적용시킬 수 있을까를 알아야 하는데, Splay 연산은 이 연산들을 별개의 정점에 따로따로 적용하는 것이 아닌 하나의 정점에 반복적으로 적용한다는 사실을 알아야 합니다. 한 번의 Splay 연산이 $n$개의 스텝으로 구성될 경우 $n-1$번의 Zig-zig 스텝이나 Zig-zag 스텝을 거치게 되므로,
$$
\displaystyle
\begin{eqnarray*}
AC(\mbox{Splay}(x)) &=& \sum_{i=1}^{n-1} ( AC(\zzig) \or AC(\zzag)) +AC(\zig)\\
&=& 3(r_1 -r_0) + 3(r_2 -r_1) +\cdots + 3(r_n -r_{n-1}) + 1 \\
&=& 3(r_n - r_0) +1 \\
&=& 3(r(root)-r(x))+1
\end{eqnarray*}
$$
이 됩니다. 비로소 원래 목표로 하던 정리를 증명했습니다! 또한 이 정리로부터 다음 명제도 자연스레 이끌어낼 수 있습니다. (rank는 항상 트리의 정점 개수에 $\lg$를 씌운 것보다 클 수 없으니까요.)

**Corollary 6.** $AC(\mbox{Splay}(x)) \le 3 \lg  n +1$ 이다.

따라서 Splay 연산은 amortized $O(\lg n)$ 이라는 결론을 이끌어 낼 수 있습니다.



### 기타 다른 연산

Splay의 시간복잡도가 $O(\lg n)$이라는 것을 깨달으면, 나머지 연산은 간단하게 해결됩니다. Splay Tree는 삽입과 삭제, 검색한 노드에 항상 Splay 연산을 진행해 줍니다. 이 세 연산의 시간복잡도는 항상 Splay 연산에 의해 Bound되게 됩니다. 그 이유는 매우 간단한데, 이 세 연산을 수행하는 동안 사용해야 하는 연산량이 반드시 그 정점에 대한 Splay 연산보다 시간복잡도가 같거나 더 작기 때문입니다. 모든 연산이 연산에서 Splay한 정점의 높이를 초과하는 시간복잡도를 가질 수 없다는 말과 같게 생각해도 좋습니다. 이 세 연산 외에도 Splay를 통해 Bound 될 수 있는 연산이라면, 항상 $O(\lg n)$ 복잡도로 수행할 수 있다는 것을 간단하게 증명할 수 있습니다.



## Segment Tree Beats

Segment Tree Beats는 일반적인 세그먼트 트리 Lazy Propagation에서 좀 더 나아가, 노드에 정보를 추가적으로 저장하고 이를 바탕으로 보다 복잡한 연산들의 시간복잡도를 bound 하는 기법입니다. 이 문단에서도 역시 독자가 세그먼트 트리 비츠의 기본적인 내용을 알고 있다고 간주하고 시간복잡도를 설명합니다. 세그먼트 트리 비츠를 대표할 만 한 2개의 문제의 시간복잡도만 분석하고 설명할 것입니다만, 다른 문제에도 비슷한 방식으로 접근하여 복잡도를 증명할 수 있습니다.



### Range Min Query

배열에서 다음과 같은 쿼리 두 개를 처리해야 합니다.

1. $i \in [l,r]$에 대해서 $A_i$를 $\min (A_i,x)$로 바꾼다.
2. $\displaystyle \sum_{i=L}^{R} A_i$를 구한다.

이 두 쿼리에 대한 amortized time complexity는 $O(\lg n)$ 입니다. 이를 어떻게 증명할 수 있을까요? 이는 세그먼트 트리 비츠 특유의 tag와 break로 증명할 수 있습니다. 세그먼트 트리 비츠에서의 tag는 Lazy Propagation에서 사용하는 Lazy 값과 유사한 기능을 하는데요, 어떤 노드에 tag가 달려있다는 것은 그 노드가 담당하는 범위의 값을 그 tag로 대체할 수 있다는 뜻입니다.

이 문제에서의 tag를 설정합시다. 맨 처음에는 자신이 담당하는 범위에서 가장 큰 값을 tag로 하고, 만약 자신의 부모 정점과 tag 값이 같다면 자신의 tag 값을 비운 채로 남깁니다. 이 글을 읽는 독자라면 이 문제를 세그먼트 트리 비츠로 해결할 때는 maximum 값과 second maximum 값을 같이 저장해서 문제를 해결한다는 것을 알고 계실겁니다. 방금 전에 설정한 것처럼 tag를 나타내면, 어떤 정점이 나타내는 구간에서의 second maximum 값은 서브트리에 있는 tag들 중 maximum인 것 또한 알 수 있습니다. 이 tag가 중요한 이유는, 이 tag가 서브트리에 존재하는 모든 tag보다 strict하게 크다는 성질이 1번 쿼리 이후에도 성립한다는 점입니다. 왜냐하면 $x$가 그 정점의 maximum 값 또는 second maximum 값보다 크다면 그 정점에 아무 일도 일어나지 않거나 그 정점의 tag만 변경될 것이고, 반대로 만약 어떤 정점의 second maximum보다 $x$가 작다면 직접 아래로 내려가서 조건을 만족하도록 정점에 대한 값들을 새로 갱신해 줄 것이기 때문입니다. 요약하면 이 성질이 만족되지 않을 때는 직접 하위 정점들을 방문해서 성질을 만족시키도록 값을 유지해 주니까, 어떤 정점에 tag를 다는 것은 서브트리에 있는 모든 정점들이 자신보다 작은 tag를 가지고 있는 경우 뿐입니다.

 그렇다면 이 tag가 언제 바뀌는지 알아봅시다. 첫 번째로 방금 전에 설명했듯이 1번 쿼리를 수행하면 조건을 만족시키지 않는 몇 가지 태그를 제거하고, 또 몇 개의 새로운 태그를 추가하게 될 것입니다. 그리고 Lazy Propagation처럼 위에 있는 tag가 아래에 영향을 미치게 된다면, 그 때도 아래에 있는 정점들을 향해 tag가 갱신이 되겠죠. (이를 앞으로 pushdown이라고 칭하겠습니다.)

 조금 더 분석을 원할하게 하기 위해, tag의 클래스를 다음과 같이 정의합시다.

1. 한 번의 구간 최솟값 쿼리를 통해서 붙여진 모든 태그는 같은 클래스에 속합니다.
2. pushdown 후에 내려간 새 tag는 기존에 있던 tag와 같은 클래스에 속합니다.
3. 위 두 조건을 둘 다 만족시키지 못하는 두 tag는 다른 클래스에 속합니다.

또 1번 쿼리를 수행할 때 우리가 실제로 방문하는 정점들을 일반 정점, 조건에 걸려서(쿼리의 범위에 속하지 않아서) 방문하지 않는 정점들을 잉여 정점들이라고 칭하겠습니다.

 이러한 복잡한 과정을 걸쳐서 드디어 이 세그먼트 트리에 대한 Potential Function을 정의할 수 있습니다. tag 클래스 $T$에 대해 $w(T)$를 서브트리 안에 적어도 하나의 tag 클래스가 $T$인 정점을 가지고 있는 정점의 수라고 합시다.  또 Potential Function $\Phi (x)$를 존재하는 모든 tag 클래스에 대해 $w(T)$의 합으로 정의합시다.

세그먼트 트리에 $n$개 초과의 tag가 존재할 수 없음은 너무 자명합니다. 따라서 $\Phi (x)$는 처음에 $O(n \lg n)$의 값을 가집니다. 이제 각 연산이 Potential Function을 어떻게 변화시키는지 살펴봅시다.

1. pushdown 연산을 통해 tag가 자식 정점으로 내려가게 된다면, $\Phi(x)$는 $2$만큼 증가하게 됩니다.
2. tag는 항상 일반 정점에서만 갱신됩니다. 그래서 새롭게 나타나는 tag 클래스 $T$에 대해, $w(T)$는 $O(\lg n)$의 값을 가지게 됩니다. (일반 정점의 개수가 세그먼트 트리의 성질에 의거해 많아봐야 $O(\lg n)$이기 때문입니다.)
3. 쿼리를 처리할 때 잉여 정점들을 방문하는 이유는 그 정점들의 서브트리에서 조건을 만족하지 못하는 tag 몇 개를 지워야 하기 때문입니다. 그리고 만약에 그런 서브트리에서 tag 클래스 $T$에 속하는 tag가 한 개라도 삭제된다면, 같은 tag 클래스에 속하는 tag들도 그 서브트리에서 모두 제거됨이 자명합니다. 따라서 잉여 정점을 하나 방문할 때마다, $\Phi(x)$는 적어도 $1$만큼 감소합니다.

$\Phi(x)$는 처음에 $O(n \lg n)$의 값을 가지고 $\Phi(x)$가 $O(n \lg n)$을 넘게 증가할 수 없습니다. 또 각 잉여 정점마다 $\Phi(x)$를 적어도 하나 감소시키니, 총 시간복잡도가 amortized $O(n \lg n)$이 된다는 것을 알 수 있습니다.



### More Complex Queries

배열에서 다음과 같은 4가지 쿼리를 처리해야 합니다.

1. $i \in [l,r]$에 대해서 $A_i$를 $\min (A_i,x)$로 바꾼다.

2. $i \in [l,r]$에 대해서 $A_i$를 $ \max (A_i,x)$로 바꾼다.

3. $i \in [l,r]$에 대해서 $A_i$를 $ A_i+x$로 바꾼다. 

4. $\displaystyle \sum_{i=L}^{R} A_i$를 구한다.

 이 문제에 대한 시간복잡도를 계산하기 위해서는 조금 더 tricky한 Potential Function을 잡아 줄 필요가 있습니다. tag $t$에 대해 $d(t)$를 $t$의 세그먼트 트리 내 깊이라고 하고, $\Phi(x)$를 모든 $d(t)$의 합이라고 합시다. 당연히 $d(t)$는 $O(\lg n)$이고, tag의 개수는 $n$을 초과하지 않습니다. 따라서 $\Phi(x)$의 초깃값은 $O(n\lg n)$입니다. 

 이제 각 연산들이 Potential Function을 어떻게 변화시키는지 알아봅시다.

1. 1번 쿼리를 수행하고 나서, 잉여 정점들에는 태그가 붙어있지 않을 것 입니다. 따라서 그냥 일반 정점들에 대해서 pushdown을 수행해 주는 것만 고려해주면 됩니다.  pushdown 이후에, 기존의 tag는 자식 정점 2개로 내려가게 되는데 이 때 $\Phi(x)$는 $O(\lg n)$ 만큼 증가할 것입니다. 세그먼트 트리의 구조에 의해 일반 정점은 최대 $O(\lg n)$개 있고, 따라서 pushdown은 매 쿼리마다 $O(\lg^2 n)$만큼 $\Phi(x)$를 증가시킬 것입니다. 이는 1번 쿼리가 아니라, 2번과 3번 쿼리에도 동일하게 적용할 수 있는 부분입니다.
2. 새로운 tag는 항상 일반 정점에서만 나타나므로 역시 $\Phi(x)$를 $O(\lg^2 n)$만큼만 증가시킵니다.
3. 각 잉여 정점은 $\Phi(x)$를 $1$만큼 감소시키는데, 잉여 정점에서 tag가 지워져도 그 정점보다 깊은 정점에 tag가 남아 있기 때문입니다. 

$\Phi(x)$의 총 증가량이 $O(n \lg ^2 n)$이고, 각 잉여 정점이 $\Phi(x)$를 $1$만큼 감소시키니 총 시간복잡도는 amortized $O(n\lg^2n)$ 입니다. 가장 재밌는 점은 Potential Function의 변화량을 계산할 때 min/max, 또는 interval add/subtract 함수의 성질을 이용한 것이 아닌 오직 tag의 성질만을 이용했다는 점입니다. 그래서 꼭 이런 특수한 함수들이 아니더라도 tag에 동일한 영향을 끼치는 연산이라면 별도의 증명 없이 그대로 적용 가능합니다. 



## 글을 마치며

Potential Method는 amortized anaylsis를 진행할 때 사용할 수 있는 강력한 도구입니다. 물론 Potential Function을 설정하고 이를 통해 연산의 Potential Function 변화량을 측정하는 것이 상당히 까다로운 일이기는 하지만, 자명해보이지만 증명하기는 힘든 정리들을 증명할 때 복잡도를 bound해야 하는 연산들이 많아질수록Potential Function을 이용하여 증명하는 것은 개개의 연산은 Ad-hoc하게 증명하지 않아도 된다는 점에서 빛을 발합니다(개인적인 의견입니다만). 비록 Problem Solving에서는 amortized analysis를 사용해야 하는 문제가 그렇게 많지 않지만,  segment tree beats와 같은 자료구조를 구현할 때 Proof by AC로만 문제를 해결하려고 하지 말고 한 번쯤은 Potential Method와 같은 도구들을 활용해 복잡도를 확실하게 계산하고 문제를 풀어보기를 바랍니다. 이상 마칩니다.



