# Jest メモ

## jest.fn()

自分で作るモック関数を定義したいときに使う。

★ コールバック関数のテストの時に便利！！！

- シンプルなモック関数を作る。
- 関数の呼び出し履歴・引数・戻り値などを追跡できる。
- 戻り値や実装を自由に設定可能。

```js
// 1. 呼び出しの回数を検証
function exampleFunction(fn) {
  fn();
  fn();
}

test('test1', () => {
  const mockFunction = jest.fn();

  // モック関数を直接呼び出します
  mockFunction();

  // モック関数を他の関数の中から呼び出します
  exampleFunction(mockFunction);

  // mockFunctionが呼び出されたことを確認します
  expect(mockFunction).toHaveBeenCalled();

  // mockFunctionが合計で3回呼び出されたことを確認します
  expect(mockFunction).toHaveBeenCalledTimes(3);
});
```

```js
// 2. 呼び出しの引数を検証
function exampleFunction(fn) {
  fn('arg1', 'arg2');
}

test('test2', () => {
  const mockFunction = jest.fn();

  // exampleFunction の中でモック関数を呼び出します
  exampleFunction(mockFunction);

  // mockFunction が 'arg1','arg2' という引数で呼び出されたことを確認します
  expect(mockFunction).toHaveBeenCalledWith('arg1', 'arg2');
});
```

```js
// 3. モック関数の戻り値を指定
function exampleFunction(fn) {
  return fn();
}

test('test3', () => {
  const mockFunction = jest.fn();

  // モック関数の戻り値を 'returnValue' と指定します
  mockFunction.mockReturnValue('returnValue');

  // exampleFunction の中でモック関数を呼び出します
  const result = exampleFunction(mockFunction);

  // mockFunction の戻り値が 'returnValue' になっていることを確認します
  expect(result).toBe('returnValue');
});

test('test4', () => {
  const mockFunction = jest.fn();

  // モック関数が呼び出されるたびに異なる値を返すように設定
  mockFunction
    .mockReturnValueOnce(10)
    .mockReturnValueOnce(20)
    .mockReturnValue(30);

  // 1回目の呼び出し
  const firstCall = mockFunction();
  expect(firstCall).toBe(10);

  // 2回目の呼び出し
  const secondCall = mockFunction();
  expect(secondCall).toBe(20);

  // 3回目以降の呼び出し
  const thirdCall = mockFunction();
  expect(thirdCall).toBe(30);

  // 4回目以降の呼び出し
  const fourthCall = mockFunction();
  expect(fourthCall).toBe(30);

  // mockFunctionが正確に4回呼び出されたことを確認
  expect(mockFunction).toHaveBeenCalledTimes(4);
});
```

```js
// 4. モック関数の実装を指定
function exampleFunction(fn) {
  return fn(5, 7);
}

test('test5', () => {
  const mockFunction = jest.fn((num1, num2) => {
    return num1 * num2;
  });

  // exampleFunction の中でモック関数を呼び出します
  const result = exampleFunction(mockFunction);

  // mockFunction の実装が正しく実行され戻り値が 35 になっていることを確認します
  expect(result).toBe(35);

  // mockFunction が引数 (5, 7) で呼び出されたことを確認します
  expect(mockFunction).toHaveBeenCalledWith(5, 7);
});
```

```js
// jest.fn() を使用した場合（上記の方法）
const mockFunction = jest.fn((num1, num2) => num1 + num2);

// mockImplementation() を使用した場合
const mockFunction = jest.fn();
mockFunction.mockImplementation((num1, num2) => num1 + num2);
```

## jest.spyOn()

既存のオブジェクトのメソッドを監視 or モックしたいときに使う

- 既存のオブジェクトのメソッドを スパイ（監視）し、呼び出しを記録。
- 元の実装をそのまま使うことも、差し替えることもできる。
- 後で元に戻す .mockRestore() が使えるのも特徴。

```js
// 1. 呼び出しの検証
// テスト対象の関数
function greet(name) {
  console.log(`Hello, ${name}!`);
  console.log(`Hi!, ${name}!`);
  console.log(`Ola!, ${name}!`);
}

// 関数が呼び出されたかを確認するテスト
test('greet関数が呼び出されたことを確認する', () => {
  // 関数のスパイ化
  const spyOnGreet = jest.spyOn(console, 'log');

  // スパイ化した関数を呼び出す
  greet('Bob');

  //console.logが呼び出されたかどうかだけを確認
  expect(spyOnGreet).toHaveBeenCalled();

  //console.logが3回呼び出されたかどうかを確認
  expect(spyOnGreet).toHaveBeenCalledTimes(3);

  //console.logが1度でも指定した引数で呼び出されているかを確認
  expect(spyOnGreet).toHaveBeenCalledWith('Hello, Bob!');

  //console.logの最後の呼び出しの引数を確認
  expect(spyOnGreet).toHaveBeenLastCalledWith('Ola!, Bob!');

  //console.logの1番目の呼び出しの引数を確認
  expect(spyOnGreet).toHaveBeenNthCalledWith(1, 'Hello, Bob!');

  //console.logの2番目の呼び出しの引数を確認
  expect(spyOnGreet).toHaveBeenNthCalledWith(2, 'Hi!, Bob!');

  // スパイを解除
  spyOnGreet.mockRestore();
});
```

```js
// 2. 呼び出しの戻り値を指定
function generateRandomNumber() {
  return Math.random();
}

test('generateRandomNumber calls Math.random', () => {
  const spy = jest.spyOn(Math, 'random').mockReturnValue(0.2291245446521175);

  const result = generateRandomNumber();

  expect(result).toBe(0.2291245446521175);

  spy.mockRestore();
});

// 呼び出された回数ごとに戻り値を指定
test('generateRandomNumber calls Math.random', () => {
  const spy = jest
    .spyOn(Math, 'random')
    .mockReturnValueOnce(0.2291245446521175)
    .mockReturnValueOnce(0.2222222222222222)
    .mockReturnValue(0.1111111111111111);

  const result1 = generateRandomNumber();
  const result2 = generateRandomNumber();
  const result3 = generateRandomNumber();
  const result4 = generateRandomNumber();

  expect(result1).toBe(0.2291245446521175);
  expect(result2).toBe(0.2222222222222222);
  expect(result3).toBe(0.1111111111111111);
  expect(result4).toBe(0.1111111111111111);

  spy.mockRestore();
});
```

```js
// 3. 呼び出しの実装を指定する
async function fetchUserGreeting(userId) {
  const response = await fetch(`/api/user/${userId}`)
  const user = await response.json()
  return `ユーザーID:${user.id}の${user.name}さん、こんにちは！`
}

test('fetchの実装を変更', async () => {
  const mockFetch =
  jest.spyOn(global,'fetch').mockImplementation(() =>
    Promise.resolve({
      json: () => Promise.resolve({ id: 1, name: 'Ken' }),
    })
  )

  const result = await fetchUserGreeting(1)

  expect(result).toBe(ユーザーID:1のKenさん、こんにちは！)
  mockFetch.mockRestore();
});
```

## jest.mock()

- ライブラリや外部依存関係を mock 化するために使用されます。
- jest.mock()を使用することでモジュール全体を mock に置き換えることができる。

```js
// テスト対象
import moment from 'moment';

export function mockTest() {
  const result = moment().format();
  return result;
}
```

```js
// テストコード
import moment from 'moment';
import {mockTest} from "./component/mockTest";

jest.mock('moment')
const mockedMoment = moment as unknown as jest.Mock;

describe('moment', () => {
  beforeEach(()=>{
    mockedMoment.mockImplementation(() => ({
    format: jest.fn().mockReturnValue('2023-06-11'),
    }))
  })

  test('mock moment', () => {
    const result = mockTest()
    expect(result).toBe('2023-06-11')
  })

  test('mock moment2', () => {
    mockedMoment.mockImplementation(() => ({
      format: jest.fn().mockReturnValue('2023-06-15'),
    }))
    const result = mockTest()
    expect(result).toBe('2023-06-15')
  })
})
```
