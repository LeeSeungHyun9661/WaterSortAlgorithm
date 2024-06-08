Water Sort Puzzle Algorithm
=============

![example](https://github.com/LeeSeungHyun9661/WaterSortAlgorithm/assets/101535408/7c31888c-ae81-4cfb-8588-6572f0d5e6e7)

**Water Sort Puzzle** 또는 **Ball Sort Puzzle**이라고 하는 이 게임은 다수의 병에 섞여있는 다른 색상의 내용물을 같은 색상끼리 정리하는 퍼즐입니다. 규칙은 다음과 같습니다.
> 1. 게임의 목표는 모든 병이 동일한 색상의 내용물을 가지는 것을 목표로 합니다.
> 2. 가장 위에 있는 색상과 동일한 색상일 경우, 그리고 빈 병일 경우에만 다른 병으로 이동이 가능합니다. 
> 3. 내용물이 이동할 때는 가장 위의 색상과 동일한 색상의 모든 내용물이 함께 이동해야 합니다.
> 4. 내용물이 병의 용량보다 많아서는 안됩니다. 

간단한 룰이지만 병의 개수와 내용물의 용량이 증가하면서 풀이의 방법이 복잡해지고 경우의 수가 많아집니다. 가장 큰 문제는 내용물이 더 이상 이동이 불가능해지는 경우도 있다는 점입니다. (이 경우에는 게임 오버로 초기화를 해야합니다. 아니면 실행 취소를 하는 방법도 있습니다.)
문제를 해결하기 위한 알고리즘은 재귀 함수를 사용하며 순서는 다음과 같습니다. 

> 정렬 함수
> * 현재 단계의 병의 배열의 **원본**을 저장합니다.
> * 병의 배열의 모든 병에 대해 반복문을 진행합니다. (**i**,**j**)
>   * **병-i** 가 **병-j**로 이동이 가능한지 확인합니다.
>      * **실행기록**에 현재 병의 상태를 기록합니다.
>      * **병-i**에서 **병-j**로 내용물을 이동시킵니다.
>      * **현재 병의 상태**가 **실행기록**에 포함되어 있는지 확인합니다. -> 무한 루프일 가능성이 있으므로 이하 과정을 생략합니다.
>      * 현재 병이 **정렬된 상태**인지 확인합니다. -> 정렬된 상태라면 함수를 종료합니다.
>        * 현재 병이 **정렬된 상태**가 아니라면 **정렬 함수**를 호출합니다. -> 이는 재귀적으로 함수 내부가 다른 함수를 호출하도록 할 것입니다.
>        * **정렬 함수**의 결과에 따라(즉, 앞선 과정에서의 반환값에 따라) 함수를 종료하거나(정렬에 성공한 경우) **실행기록**에서 **현재 병의 상태**를 제거하고 **현재 병의 상태**를 **원본**으로 되돌립니다.(이는 정렬이 불가능한 경우의 수에 대해 수를 물렸다는 것과 동일합니다.)
>  * 반복문의 모든 과정이 종료했다면 이는 더 이상 가능한 이동이 없음을 의미합니다.

따라서 이를 코드로 변경하면 다음과 같습니다.
```java
//Bottle class는 각 병에 해당합니다.
public static boolean sort(ArrayList<String> history, Bottle[] bottles, ArrayList<String> Answer) {
// 정렬 함수는 병의 목록, 실행기록, 정답 기록을 매개변수로 전달 받습니다. 또한 boolean값을 반환합니다.

	Bottle[] origin = copy(bottles);
	// origin은 병 배열의 현재 상태를 먼저 저장합니다. 이는 수를 되돌릴 수 있도록 하여 

	for (int i = 0; i < bottles.length; i++) {
		for (int j = 0; j < bottles.length; j++) {
		// 반복문을 통해 각 병에 대해 모든 경우의 수를 확인합니다.
			if (i != j && bottles[i].checkInsert(bottles[j])) {
			//i가 j와 다른 경우, 그리고 i병에서 j병으로 이동할 수 있는지 확인합니다.
				history.add(toString(bottles));
				// 병의 상태를 기록합니다.
				bottles[i].insert(bottles[j]);
				// 병i에서 병j로 내용물을 이동시킵니다.
				if (history.contains(toString(bottles))) {
				// 이동 후 병의 상태가 기록에 포함되어 있다면 무한 루프를 방지하기 위해 false를 반환합니다.
					return false;
				}
				Answer.add(i + "->" + j);
				// 정답 기록에 이동 내역을 저장합니다.
				if (isSorted(bottles)) {
				// 이동 후 병의 상태가 정렬되었는지 확인합니다.
					return true;
					// 정렬된 상태라면 true를 반환합니다.
				} else { 
					if (sort(history, bottles, Answer)) {
					// 정렬되지 않은 상태라면 다시 정렬 함수를 호출합니다. 또한 반환값을 확인합니다.
						return true;
						// true 반환 : 정렬이 완료되었음을 의미합니다!
					} else {
						bottles = copy(origin);
						// false 반환 : 무한 루프, 이동 가능한 수 없음 등으로 해당 이동이 잘못되었음을 의미합니다. 이동을 취소합니다.(원본으로 다시 되돌립니다)
						history.remove(history.size() - 1);
						Answer.remove(Answer.size() - 1);
						//실행 기록과 정답 기록에서 내용을 지웁니다.
					}
				}
			}

		}
	}
	return false;
	// 더 이상 움질일 수 없음을 의미하고 false를 반환합니다.
}
```
이를 기반으로 코드를 작성했습니다. 두 개의 클래스로 구성했으며 내용은 다음과 같습니다.

> Bottle.java
> ----
> (변수) String[] bottle : 병의 내용물을 문자열 배열로 저장합니다.
> (생성자) Bottle(String... waters) : 병의 내용물을 저장하며 길이를 결정하는 생성자입니다.
> (메서드) void print() : 병의 내용물을 출력합니다.
> (메서드) String toString() : 병의 내용물을 한줄의 문자열로 출력합니다. (이는 실행 기록 내부에 저장하기 편하게 변경하기 위함입니다.)
> (메서드) boolean checkInsert(Bottle target) : 해당 병(this)에서 대상(target)으로 이동이 가능한지 확인합니다.
> (메서드) void insert(Bottle target) : 해당 병(this)에서 대상(target)으로 내용물을 이동시킵니다.
> (메서드) void sorted() : 병이 정렬된 상태인지 확인합니다.
bottle class는 각각의 병에 대한 기능과 그 내용물에 대한 정의를 포함하고 있습니다.

> Main.java
> ----
> (메서드) void main(String[] args) : 실행함수입니다.
> (메서드) String toString(Bottle[] bottles) : 병의 배열을 모두 하나의 긴 배열로 만듭니다. (이는 실행 기록 내부에 저장하기 편하게 변경하기 위함입니다.)
> (메서드) boolean sort(ArrayList<String> history, Bottle[] bottles, ArrayList<String> Answer) : 정렬하는 함수로써 병 배열(bottles), 실행 기록(history), 정답 기록(Answer)를 매개변수로 받습니다.
> (메서드) void print(Bottle[] bottles) : 병의 배열을 출력합니다.

복잡한 내용은 아니지만 해당 내용이 **Water Sort Puzzle**을 해결하기 위한 방법으로 활용되기를 바랍니다.
