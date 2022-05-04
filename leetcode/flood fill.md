# flood fill

An image is represented by an `m x n` integer grid `image` where `image[i][j]` represents the pixel value of the image.

You are also given three integers `sr`, `sc`, and `newColor`. You should perform a **flood fill** on the image starting from the pixel `image[sr][sc]`.

To perform a **flood fill**, consider the starting pixel, plus any pixels connected **4-directionally** to the starting pixel of the same color as the starting pixel, plus any pixels connected **4-directionally** to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with `newColor`.

Return *the modified image after performing the flood fill*.

**Example 1:**

![https://assets.leetcode.com/uploads/2021/06/01/flood1-grid.jpg](https://assets.leetcode.com/uploads/2021/06/01/flood1-grid.jpg)

```
Input: image = [[1,1,1],[1,1,0],[1,0,1]], sr = 1, sc = 1, newColor = 2
Output: [[2,2,2],[2,2,0],[2,0,1]]
Explanation: From the center of the image with position (sr, sc) = (1, 1) (i.e., the red pixel), all pixels connected by a path of the same color as the starting pixel (i.e., the blue pixels) are colored with the new color.
Note the bottom corner is not colored 2, because it is not 4-directionally connected to the starting pixel.

```

**Example 2:**

```
Input: image = [[0,0,0],[0,0,0]], sr = 0, sc = 0, newColor = 2
Output: [[2,2,2],[2,2,2]]

```

**Constraints:**

- `m == image.length`
- `n == image[i].length`
- `1 <= m, n <= 50`
- `0 <= image[i][j], newColor < 216`
- `0 <= sr < m`
- `0 <= sc < n`

```jsx
const floodFill = function (image, sr, sc, newColor) {
  let answer = [...image];
  const m = image.length;
  const n = image[0].length;
  const startColor = image[sr][sc];

  function dfs(r, c) {
    if (r >= 0 && r < m && c >= 0 && c < n) {
      if (image[r][c] === startColor) {
        answer[r][c] = newColor;
        //상
        dfs(r - 1, c);
        //우
        dfs(r, c + 1);
        //하
        dfs(r + 1, c);
        //좌
        dfs(r, c - 1);
      }
    } else {
      return;
    }
  }

  dfs(sr, sc);

  return answer;
};

console.log(
  floodFill(
    [
      [1, 1, 1],
      [1, 1, 0],
      [1, 0, 1],
    ],
    1,
    1,
    2
  )
); // [[2,2,2],[2,2,0],[2,0,1]]

console.log(
  floodFill(
    [
      [0, 0, 0],
      [0, 0, 0],
    ],
    0,
    0,
    2
  )
); // [[2,2,2],[2,2,2]]

console.log(
  floodFill(
    [
      [0, 0, 0],
      [0, 1, 1],
    ],
    1,
    1,
    1
  )
);
```

```jsx
const image = [
  [0, 0, 0],
  [0, 0, 0]
];

const floodFill = function (image, sr, sc, newColor) {
  let answer = [...image];
  const m = image.length;
  const n = image[0].length;
  const startColor = image[sr][sc];

  function dfs(r, c) {
    if (r >= 0 && r < m && c >= 0 && c < n) {
      let pixel = new Pixel(r, c);
      console.log(pixel);
      if (image[r][c] === startColor) {
        answer[r][c] = newColor;
        //상
        dfs(r - 1, c);
        //우
        dfs(r, c + 1);
        //하
        dfs(r + 1, c);
        //좌
        dfs(r, c - 1);
      }
    } else {
      return;
    }
  }

  dfs(sr, sc);

  return answer;
};

class Pixel {
  constructor(r, c) {
    this.color = image[r][c];
    this.visited = false;
    this.top = null;
    this.bottom = null;
    this.left = null;
    this.right = null;
  }
}

class DFS {
  constructor() {
    this.start = null;
  }
  insert(r,c) {
    let newPixel = new Pixel(r,c)
    if(this.start === null) {
      this.start = newPixel;
      return this;
  } else {
    let current = this
  }
  isVisited(r,c) {

  }
}

// console.log(
//   floodFill(
//     [
//       [1, 1, 1],
//       [1, 1, 0],
//       [1, 0, 1]
//     ],
//     1,
//     1,
//     2
//   )
// ); // [[2,2,2],[2,2,0],[2,0,1]]

console.log(
  floodFill(
    [
      [0, 0, 0],
      [0, 0, 0]
    ],
    0,
    0,
    2
  )
); // [[2,2,2],[2,2,2]]

// console.log(
//   floodFill(
//     [
//       [0, 0, 0],
//       [0, 1, 1]
//     ],
//     1,
//     1,
//     1
//   )
// );
```

---

```jsx
const floodFill = function (image, sr, sc, newColor) {
  let answer = [...image];
  const m = image.length;
  const n = image[0].length;
  const startColor = image[sr][sc];

  let graphDfs = new DFS();
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      graphDfs.insert(i, j);
    }
  }

  function dfs(r, c) {
    let currentPixel = graphDfs.list.find(
      (pixel) => pixel.r === r && pixel.c === c
    );
    if (r >= 0 && r < m && c >= 0 && c < n && !currentPixel.visited) {
      if (image[r][c] === startColor) {
        graphDfs.visit(r, c);
        answer[r][c] = newColor;
        //상
        dfs(r - 1, c);
        //우
        dfs(r, c + 1);
        //하
        dfs(r + 1, c);
        //좌
        dfs(r, c - 1);
      }
    } else {
      return;
    }
  }

  dfs(sr, sc);

  return answer;
};

class Pixel {
  constructor(r, c) {
    this.r = r;
    this.c = c;
    this.visited = false;
  }
}

class DFS {
  constructor() {
    this.start = null;
    this.list = [];
  }
  insert(r, c) {
    let newPixel = new Pixel(r, c);
    if (this.start === null) {
      this.start = newPixel;
    }
    this.list.push(newPixel);
    return this;
  }
  visit(r, c) {
    this.list.find((pixel) => pixel.r === r && pixel.c === c).visited = true;
  }
}

// console.log(
//   floodFill(
//     [
//       [1, 1, 1],
//       [1, 1, 0],
//       [1, 0, 1]
//     ],
//     1,
//     1,
//     2
//   )
// ); // [[2,2,2],[2,2,0],[2,0,1]]

// console.log(
//   floodFill(
//     [
//       [0, 0, 0],
//       [0, 0, 0]
//     ],
//     0,
//     0,
//     2
//   )
// ); // [[2,2,2],[2,2,2]]

console.log(
  floodFill(
    [
      [0, 0, 0],
      [0, 1, 1],
    ],
    1,
    1,
    1
  )
); // [[0,0,0],[0,1,1]]
```

---

제출

```jsx
const floodFill = function (image, sr, sc, newColor) {
  let answer = [...image];
  const m = image.length;
  const n = image[0].length;
  const startColor = image[sr][sc];

  let graph = new Graph();
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      graph.insert(i, j);
    }
  }

  function dfs(r, c) {
    let currentPixel = graph.list.find(
      (pixel) => pixel.r === r && pixel.c === c
    );
    if (r >= 0 && r < m && c >= 0 && c < n && !currentPixel.visited) {
      if (image[r][c] === startColor) {
        graph.visit(r, c);
        answer[r][c] = newColor;
        dfs(r - 1, c);
        dfs(r, c + 1);
        dfs(r + 1, c);
        dfs(r, c - 1);
      }
    } else {
      return;
    }
  }

  dfs(sr, sc);

  return answer;
};

class Pixel {
  constructor(r, c) {
    this.r = r;
    this.c = c;
    this.visited = false;
  }
}

class Graph {
  constructor() {
    this.list = [];
  }
  insert(r, c) {
    let newPixel = new Pixel(r, c);
    this.list.push(newPixel);
    return this;
  }
  visit(r, c) {
    this.list.find((pixel) => pixel.r === r && pixel.c === c).visited = true;
  }
}
```

---

클래스말고 객체 사용

```jsx
const floodFill = function (image, sr, sc, newColor) {
  let answer = [...image];
  const m = image.length;
  const n = image[0].length;
  const startColor = image[sr][sc];
  let isVisited = {};

  function dfs(r, c) {
    const currentPixel = `r${r}c${c}`;
    if (r >= 0 && r < m && c >= 0 && c < n && !isVisited[currentPixel]) {
      if (image[r][c] === startColor) {
        isVisited[currentPixel] = true;
        answer[r][c] = newColor;
        dfs(r - 1, c);
        dfs(r, c + 1);
        dfs(r + 1, c);
        dfs(r, c - 1);
      }
    } else {
      return;
    }
  }

  dfs(sr, sc);

  return answer;
};
```
