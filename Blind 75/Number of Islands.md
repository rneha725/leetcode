Question: https://leetcode.com/problems/number-of-islands/

Went through each `1` in the matrix and see what all nbrs I can visit and inserted them into visited set. Once a coordinate is done, tried finding the next coordinate with value `1` and not in the visited set. Counted these occurences.
```cpp
class Solution {
public:
		int nRow, nCol;
		void dfs(int row, int col, vector<vector<char>>& grid, vector<vector<int>> &visited) {
			visited[row][col] = 1;
			vector<int> rowDirections = {0,1,0,-1};
			vector<int> colDirections = {1, 0, -1, 0};

			//exploring nbrs
			for(int i = 0; i < rowDirections.size(); i++) {
				int next_row = row + rowDirections[i];
				int next_col = col + colDirections[i];
				bool isValid = (next_row < nRow && next_row >= 0 && next_col < nCol && next_col >= 0);
				if(isValid && visited[next_row][next_col] == 0 && grid[next_row][next_col] == '1') {
					dfs(next_row, next_col, grid);
				}
			}
		}	

		int numIslands(vector<vector<char>>& grid) {
			nRow = grid.size(); nCol = grid[0].size();
			vector<vector<int>> visited = vector<vector<int>>(nRow, vector<int>(nCol, 0));
			int count = 0;
			for(int i = 0; i < nRow; i++) {
				for(int j = 0; j < nCol; j++) {
					if(visited[i][j] == 0 && grid[i][j] == '1') {
						dfs(i, j, grid, visited);
						count++;
					}
				}
			}
			return count;
		}

};
```