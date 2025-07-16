<template>
  <div id="root">
    <div class="board">
      <div
        v-for="(value, index) in 11"
        :key="index"
        :style="{
          backgroundColor: 'white',
          gridRow: '1 / span 10',
          gridColumn: index,
          width: '1px',
          height: '100%',
          zIndex: 2,
        }"
      ></div>
      <div
        v-for="(value, index) in 11"
        :key="index"
        :style="{
          backgroundColor: 'white',
          gridRow: index,
          gridColumn: '1 / span 10',
          width: '100%',
          height: '1px',
          zIndex: 2,
        }"
      ></div>
      <div
        class="car"
        :style="{ gridColumn: 2, gridRow: 2, backgroundColor: 'red' }"
      ></div>
      <div
        class="goal"
        :style="{ gridColumn: 10, gridRow: 10, backgroundColor: 'white' }"
      ></div>
    </div>
    <div class="btns">
      <button class="btn" @click="bfs(this.start)">BFS</button>
      <button class="btn" @click="dfs">DFS</button>
      <button class="btn" @click="reload">새로고침</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      location: Array.from({ length: 10 }, () =>
        Array.from({ length: 10 }, () => null)
      ),
      visited: Array.from({ length: 10 }, () =>
        Array.from({ length: 10 }, () => false)
      ),
      dx: [1, -1, 0, 0],
      dy: [0, 0, 1, -1],

      stack: [],
      queue: [],
      walls: [
        [1, 0],
        [2, 0],
        [3, 0],
        [4, 0],
        [5, 0],
        [6, 0],
        [7, 0],
        [8, 0],
        [9, 0],
        [9, 1],
        [9, 2],
        [1, 2],
        // [2, 2],
        // [3, 2],
        // [4, 2],
        [5, 2],
        [6, 2],
        // [7, 2],
        [8, 2],
        [9, 2],

        [0, 4],
        [1, 4],
        [2, 5],
        [3, 5],
        [4, 6],
        [5, 6],
        [6, 6],
        [7, 6],
        // [8, 6],
        // [8, 7],
        // [8, 8],

        [3, 3],
        [4, 3],
        [5, 4],
        [6, 4],
        // [7, 4],
        [8, 4],
        // [9, 4],
        // [9, 7],
        // [9, 6],
        // [6, 8],
        [7, 9],
      ],
      found: false,
      prev: Array.from({ length: 10 }, () => {
        return Array.from({ length: 10 }, () => null);
      }),
      start: {
        x: 1,
        y: 1,
      },
      back: [],
    };
  },
  mounted() {
    const $board = document.querySelector('.board');
    for (let i = 0; i < 10; i++) {
      for (let j = 0; j < 10; j++) {
        const div = document.createElement('div');
        div.style.backgroundColor = 'black';
        div.style.width = '100%';
        div.style.height = '100%';
        div.style.gridRow = i + 1;
        div.style.gridColumn = j + 1;
        div.style.zIndex = '-1';

        this.location[i][j] = div;
        $board.appendChild(div);
      }
    }

    for (const [x, y] of this.walls) {
      this.location[x][y].style.backgroundColor = 'blue';
      this.location[x][y].style.zIndex = 0;
    }
  },
  methods: {
    dfs() {
      const x = this.start.x;
      const y = this.start.y;
      this.back = [];
      this.visited.forEach((row) => row.fill(false));

      if (this.recursiveDFS(x, y)) {
        this.animate();
      } else {
        alert('못 찾음!');
      }
    },

    recursiveDFS(x, y) {
      if (this.visited[x][y]) return false;
      if (this.location[x][y].style.backgroundColor === 'blue') return false;
      if (x === 9 && y === 9) return true;
      this.visited[x][y] = true;
      for (let i = 0; i < this.dx.length; i++) {
        let nextX = this.dx[i] + x;
        let nextY = this.dy[i] + y;
        if (nextX < 0 || nextX >= 10 || nextY < 0 || nextY >= 10) continue;

        if (this.recursiveDFS(nextX, nextY)) {
          this.back.push([nextX, nextY]);
          return true;
        }
      }
      return false;
    },

    animate() {
      let index = 0;
      this.back.reverse();
      const car = document.querySelector('.car');

      const interval = setInterval(() => {
        if (index >= this.back.length) {
          clearInterval(interval);
          return;
        }

        const [x, y] = this.back[index];
        car.style.gridRow = x + 1;
        car.style.gridColumn = y + 1;
        index++;
      }, 300);
    },

    bfs(start) {
      this.visited.forEach((row) => row.fill(false));
      this.queue = [];
      this.back = [];

      const car = document.querySelector('.car');

      const toGrid = (n) => {
        return n + 1;
      };
      this.visited[start.x][start.y] = true;
      this.queue.push(start);

      const interval = setInterval(() => {
        const location = this.queue.shift();

        car.style.gridColumn = toGrid(location.y);
        car.style.gridRow = toGrid(location.x);

        if (location.x === 9 && location.y === 9) {
          alert('도착');
          clearInterval(interval);
          return;
        }

        for (let i = 0; i < this.dx.length; i++) {
          let nextX = this.dx[i] + location.x;
          let nextY = this.dy[i] + location.y;

          if (nextX < 0 || nextX >= 10 || nextY < 0 || nextY >= 10) continue;
          if (this.location[nextX][nextY].style.backgroundColor === 'blue')
            continue;
          if (this.visited[nextX][nextY]) continue;

          const next = {
            x: nextX,
            y: nextY,
          };
          this.queue.push(next);
          this.back.push(next);
          this.visited[nextX][nextY] = true;
        }
      }, 300);
    },
    reload() {
      window.location.reload();
    },
  },
};
</script>

<style scoped>
.board {
  display: grid;
  grid-template-columns: repeat(10, 1fr);
  grid-template-rows: repeat(10, 1fr);
  border: 3px solid red;
  width: 90vmin; /* 뷰포트 기준으로 정사각형 만들기 */
  height: 90vmin;
  max-width: 800px; /* 최대 크기 제한 */
  max-height: 800px;
}
#root {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw; /* 전체 너비 */
  height: 100vh;
  margin: auto;
  background-color: black;
  flex-direction: column;
  gap: 20px;
}
.btn {
  border-radius: 30px;
  margin-top: 15px;
  font-size: 15x;
  width: 90vmin; /* 뷰포트 기준으로 정사각형 만들기 */
  height: 90vmin;
  width: 80px;
  height: 45px;
  font-size: 16px;
  transition: 0.3s;
  background-color: #ff6363;
  color: white;
  z-index: 1000;
  border: none;
  cursor: pointer;
}
.car {
  z-index: 3;
}
.btns {
  display: flex;
  gap: 30px;
}
</style>
