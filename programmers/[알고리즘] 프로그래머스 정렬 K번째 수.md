# 정렬 - K번째 수

## 문제 설명

배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.

예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면

1. array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.
2. 1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.
3. 2에서 나온 배열의 3번째 숫자는 5입니다.

배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

## 제한 사항

- array의 길이는 1 이상 100 이하입니다.
- array의 각 원소는 1 이상 100 이하입니다.
- commands의 길이는 1 이상 50 이하입니다.
- commands의 각 원소는 길이가 3입니다.

## 입출력 예

![Untitled](%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%A7%E1%86%AF%20-%20K%201f4ba/Untitled.png)

## 입출력 예 설명

[1, 5, 2, 6, 3, 7, 4]를 2번째부터 5번째까지 자른 후 정렬합니다. [2, 3, 5, 6]의 세 번째 숫자는 5입니다.[1, 5, 2, 6, 3, 7, 4]를 4번째부터 4번째까지 자른 후 정렬합니다. [6]의 첫 번째 숫자는 6입니다.[1, 5, 2, 6, 3, 7, 4]를 1번째부터 7번째까지 자릅니다. [1, 2, 3, 4, 5, 6, 7]의 세 번째 숫자는 3입니다.

## 코딩 과정

처음에 splice를 사용하였더니 원래 배열인 array에도 영향을 줘서 slice를 사용하였다.

---

## 소스코드

```jsx
const array = [1, 5, 2, 6, 3, 7, 4];
const commands = [
  [2, 5, 3],
  [4, 4, 1],
  [1, 7, 3],
];
//return [5,6,3]

function solution(array, commands) {
  let answer = [];
  let i = 0;
  let j = 0;
  let k = 0;
  const commL = commands.length;
  for (let a = 0; a < commL; a++) {
    i = commands[a][0];
    j = commands[a][1];
    k = commands[a][2];
    const newArr = array.slice(i - 1, j);
    answer.push(newArr.sort((a, b) => a - b)[k - 1]);
  }
  return answer;
}

console.log(solution(array, commands));
```
