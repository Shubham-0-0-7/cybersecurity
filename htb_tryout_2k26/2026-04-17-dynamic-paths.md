```python
#!/usr/bin/env python3
from pwn import *

# Function to calculate the minimum path sum using Dynamic Programming
def get_min_path(grid, rows, cols):
    # Initialize a DP table with zeros
    dp = [[0] * cols for _ in range(rows)]
    
    # Base case: start point
    dp[0][0] = grid[0][0]
    
    # Fill out the first row (can only come from the left)
    for c in range(1, cols):
        dp[0][c] = dp[0][c-1] + grid[0][c]
        
    # Fill out the first column (can only come from above)
    for r in range(1, rows):
        dp[r][0] = dp[r-1][0] + grid[r][0]
        
    # Fill out the rest of the grid
    for r in range(1, rows):
        for c in range(1, cols):
            # Take the current grid value + the minimum of the path above or to the left
            dp[r][c] = grid[r][c] + min(dp[r-1][c], dp[r][c-1])
            
    # The bottom-right corner holds our final minimum sum
    return dp[rows-1][cols-1]

# Connect to the CTF server
host = '154.57.164.76'
port = 30733
io = connect(host, port)

# Loop through all 100 tests
for i in range(1, 101):
    log.info(f"Solving Test {i}/100...")
    
    # Wait for the "Test X/100" prompt to appear
    io.recvuntil(f"Test {i}/100\n".encode())
    
    # 1. Parse Dimensions
    # Looks like: "3 3"
    dims = io.recvline().decode('utf-8').strip().split()
    rows = int(dims[0])
    cols = int(dims[1])
    
    # 2. Parse the Grid Data
    # Looks like: "8 8 8 4 8 4 3 3 8"
    flat_data = io.recvline().decode('utf-8').strip().split()
    flat_data = [int(x) for x in flat_data]
    
    # 3. Convert flat list into a 2D array (list of lists)
    grid = []
    for r in range(rows):
        start_idx = r * cols
        end_idx = start_idx + cols
        grid.append(flat_data[start_idx:end_idx])
        
    # 4. Calculate the answer
    answer = get_min_path(grid, rows, cols)
    log.success(f"Calculated Minimum Sum: {answer}")
    
    # 5. Send the answer back
    io.sendlineafter(b"> ", str(answer).encode())

# After 100 successful answers, grab the flag!
io.interactive()
```
