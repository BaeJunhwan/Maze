# Maze Project

Eller's algorithm을 활용하여 랜덤 미로를 생성하고, 생성된 `.maz` 파일을 openFrameworks 환경에서 시각화하는 프로젝트입니다.

미로의 각 칸을 하나의 vertex로 보고, 벽이 없어 이동 가능한 통로를 edge로 해석하여 인접 리스트 형태로 저장했습니다. 이후 DFS와 BFS 탐색 알고리즘을 적용하여 출발점에서 도착점까지의 탈출 경로와 탐색 과정에서 방문한 경로를 화면에 표시하도록 구현했습니다.

## Project Structure

```text
Maze/
├── generator/
│   ├── maze.c
│   ├── maze.h
│   └── Makefile
├── visualizer/
│   ├── main.cpp
│   ├── ofApp.cpp
│   ├── ofApp.h
│   ├── ofxWinMenu.cpp
│   └── ofxWinMenu.h
└── maze.maz
```

## Features

* Eller's algorithm 기반 랜덤 미로 생성
* 오른쪽 벽과 아래쪽 벽 정보를 분리하여 미로 구조 관리
* `.maz` 파일 형식으로 미로 출력
* openFrameworks를 활용한 `.maz` 파일 시각화
* 미로의 각 칸을 vertex, 이동 가능한 통로를 edge로 해석
* 인접 리스트를 활용한 미로 그래프 구조 저장
* DFS를 이용한 미로 탈출 경로 탐색
* BFS를 이용한 미로 탈출 경로 탐색
* 탐색 중 방문한 경로와 최종 탈출 경로를 서로 다른 색으로 시각화
* 출발점과 도착점을 별도의 사각형으로 표시
* 메뉴 및 버튼을 통한 DFS/BFS 탐색 실행

## Development Environment

### Maze Generator

* Language: C
* Environment: Linux/Unix terminal
* Compiler: GCC
* Tool: PuTTY

### Maze Visualizer

* Language: C++
* Framework: openFrameworks
* IDE: Visual Studio
* OS: Windows

## Build & Run

### Maze Generator

```bash
cd generator
make
./a.out
```

프로그램 실행 후 미로의 가로 크기와 세로 크기를 숫자로 입력하면 `.maz` 형식의 미로 파일을 생성할 수 있습니다.

### Maze Visualizer

본 시각화 프로그램은 openFrameworks 환경에서 실행됩니다.
`visualizer/` 폴더의 Visual Studio solution 파일을 열어 실행한 뒤, `.maz` 파일을 불러오면 미로가 화면에 출력됩니다.

이후 화면 상단의 DFS 또는 BFS 버튼을 누르거나, 메뉴에서 해당 기능을 선택하면 미로 탐색 결과를 확인할 수 있습니다.

## Algorithm

### Maze Generator

미로 생성에는 Eller's algorithm을 사용했습니다. 각 칸의 집합 정보를 관리하면서 오른쪽 벽과 아래쪽 벽을 조건에 따라 제거하고, 모든 행이 연결성을 유지하도록 구성했습니다. 이를 통해 무작위성을 가지면서도 출발점에서 도착점까지 이동 가능한 미로를 생성했습니다.

### Maze Visualizer

시각화 프로그램에서는 `.maz` 파일을 읽어 각 방을 하나의 정점으로 변환했습니다.
정점 번호는 다음과 같이 계산했습니다.

```cpp
idx = row * maze_col + col;
```

각 방의 오른쪽 또는 아래쪽에 벽이 없는 경우 두 정점을 서로 연결하여 인접 리스트에 저장했습니다.

```cpp
vector<vector<int>> adjList;
```

이를 통해 미로를 그래프 탐색 알고리즘에 적용할 수 있는 형태로 변환했습니다.

### DFS Search

DFS는 재귀 방식이 아니라 `stack<int>`를 이용한 iterative 방식으로 구현했습니다.
시작 정점에서 한 방향으로 깊게 탐색하고, 더 이상 방문할 수 있는 인접 정점이 없으면 stack에서 pop하여 이전 정점으로 되돌아가도록 처리했습니다.

탐색 중 실제로 이동한 간선은 `visitedEdge`에 저장하고, 최종 경로 복원을 위해 `parent` 배열을 사용했습니다.

### BFS Search

BFS는 `queue<int>`를 이용하여 구현했습니다.
시작 정점에서 가까운 정점부터 차례대로 탐색하며, 방문하지 않은 인접 정점을 queue에 삽입합니다.

BFS는 무가중치 그래프에서 최단 경로를 보장하므로, 도착점을 처음 발견했을 때 `parent` 배열을 이용해 출발점부터 도착점까지의 경로를 복원했습니다.

## Visualization

탐색 결과는 다음과 같이 화면에 표시됩니다.

* 회색 선: DFS 또는 BFS 탐색 과정에서 방문한 경로
* 빨간색 선: 출발점에서 도착점까지의 최종 탈출 경로
* 초록색 사각형: 출발점
* 파란색 사각형: 도착점

탐색 중 방문한 경로는 정점 순서가 아니라 실제 이동한 edge를 저장하여 그리도록 구현했습니다. 이를 통해 DFS가 막다른 길에서 되돌아가는 경우에도 존재하지 않는 경로가 화면에 그려지지 않도록 처리했습니다.

## What I Learned

이 프로젝트를 통해 미로 생성 알고리즘이 실제 프로그램에서 어떻게 구현되는지 이해할 수 있었습니다. 특히 Eller's algorithm을 사용하면서 각 칸의 집합 정보를 관리하고, 벽을 제거하는 방식으로 미로를 구성하는 과정을 경험했습니다.

또한 생성된 `.maz` 파일을 openFrameworks에서 읽어 화면에 시각화하면서 파일 입출력과 그래픽 출력 과정이 어떻게 연결되는지 배웠습니다.

3주차 구현을 통해 미로의 각 칸을 vertex, 이동 가능한 통로를 edge로 해석하여 인접 리스트로 저장하고, 이를 기반으로 DFS와 BFS 탐색을 수행했습니다. DFS에서는 stack을 이용한 iterative 탐색 방식을 구현하면서 재귀 DFS와 명시적 stack 사용 방식의 차이를 이해할 수 있었습니다. BFS에서는 queue를 이용하여 가까운 정점부터 탐색하는 방식과 `parent` 배열을 통한 최단 경로 복원 과정을 확인했습니다.

특히 단순히 방문한 정점 순서를 저장하는 방식으로는 DFS 탐색 경로를 정확히 그리기 어렵다는 점을 확인했고, 실제로 이동한 간선을 `visitedEdge`에 저장하여 탐색 경로를 시각화하는 방식으로 수정했습니다. 이를 통해 그래프 탐색 알고리즘의 결과를 화면에 표현할 때 자료구조 설계가 중요하다는 점을 배웠습니다.
