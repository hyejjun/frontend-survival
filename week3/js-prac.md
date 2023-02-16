# JS - 배열 활용하기

products는 배열이라고 가정하고.

## Array.reduce()

[Array.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

```jsx
/* reduce 기능을 이용하여 중복되지 않게 카테고리만 분리했다. */
const categories = products.reduce(
  (acc: string[], product: Product) =>
   (acc.includes(product.category)
      ? acc
      : [...acc, product.category]),
    [],
);
```

배열.reduce((누적값, 현잿값, 인덱스, 요소) => { return 결과 }, 초깃값);

## Array.includes()

[Array.includes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)

includes() 메서드는 배열이 특정 요소를 포함하고 있는지 판별합니다.

- 배열 acc에 값이 누적되고 해당 배열을 반환한다.
- product 현재 값이다. 배열의 요소 중 하나가 될 것 이다.
- acc 누적 배열에 product의 category가 있는지 확인하고, 값이 있다면 현재 acc를 유지하고 값이 없다면 기존 acc에 현재 값인 product의 category를 추가한다.

## Array.filter()

[Array.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

```jsx
const category = "Fruits";

/*filter 기능을 이용하여 원하는 값들만 모아 새로운 배열에 담아낸다.*/
products.filter((product) =>
 product.category === category)
 .map((product) => (
    <tr key={product.name}>
      <td>{product.name}</td>
      <td>{product.price}</td>
    </tr>
  ))
```

- filter 기능을 이용하여 category = 'Fruits' 와 일치하는 요소들만 뽑아낸다.
- 자바스크립트에서 filter 는 배열에 사용하며, 주어진 함수를 만족하는 모든 요소를 모아 새 배열로 반환한다.
- 이후에 조건에 부합하는 배열만 가지고 다시 map을 돌린다.
