main function logic .. disassembled in rizin cutter   
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5e10c44c-7a99-4f9e-a059-45b675c71f67" />
so starting off we open the binary in rizin cutter to see what is going on. looking at the main function graph,   
we can see it sets up three variables on the stack and initializes them all to zero. this is basically our starting x, y, and z coordinates at 0, 0, 0.   
after that, it drops into a loop. it calls a function named get_cell to check our current location, and then it compares a value inside   
that cell at offset 0xc to the number 3. if it is not 3, it asks us for a direction to move, updates our coordinates, and loops back around. if it is exactly 3,   
we break out of the loop, get a success message about breaking into the vault, and it calls get_flag. so the whole goal of the program is to navigate 
from 0,0,0 until we land on a cell that is marked as type 3.

now below given is get_flag CFG ...  
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d29a81cb-d45c-4b2a-934d-e2b18e922473" />



also get_cell   
<img width="464" height="329" alt="image" src="https://github.com/user-attachments/assets/ad2bb582-d3ff-4a48-b031-4e3bcf71f246" />   
to figure out how to get there, we have to look at how get_cell works.  
the assembly in that function is just doing a bunch of math to calculate our exact memory address in the maze based on our coordinates.   
it takes our x coordinate and does some load effective address instructions to multiply it by 5, then by 5 again,   
and then shifts it to multiply it by 16. that basically means x is multiplied by 400. then it takes y, multiplies it by 5, then by 4,   
meaning y is multiplied by 20. then it adds z. finally it shifts the whole thing left by 4, which multiplies the entire combined offset by 16.  
it adds this final offset to a base address of 0x20e0, which is where the maze data is physically stored in the binary    
this brings us to extracting the maze data. you mentioned 128 bytes, but it is actually 128,000 bytes.   
if you multiply the grid size 20 x 20 x 20, you get 8000 total cells in the game. multiply those 8000 cells by 16 bytes per cell,  
and you get exactly 128,000 bytes for the entire maze structure.


to pull this out of the binary, we used the dd command in linux. the command `dd if=./tunnel of=maze.bin bs=1 skip=8416 count=128000` does exactly this.   
"if" is the input file, "of" is the output file. "bs=1" tells it to read one byte at a time. "skip=8416" tells it to skip the first 8416 bytes of the binary   
(which is 0x20e0 in decimal, the base address of our maze that we found in get_cell). finally, "count=128000" tells it to extract exactly that many bytes and stop. 
this isolates the raw maze data into a new file called maze.bin so we can write a script to find the path without having to play it blind.    


to actually solve it and get the flag, we parse that maze.bin file we dumped and run a breadth first search to find the shortest path from 0,0,0 to the exit cell marked as 3.

```python
from collections import deque
import struct

with open("maze.bin", "rb") as f:
    raw_maze = f.read()

def get_cell_type(x, y, z):
    if not (0 <= x < 20 and 0 <= y < 20 and 0 <= z < 20):
        return 2
    
    offset = 16 * (400 * x + 20 * y + z)
    cell_data = raw_maze[offset+12 : offset+16]
    return struct.unpack('<I', cell_data)[0]

def solve_maze():
    queue = deque([(0, 0, 0, [])])
    visited = set()
    visited.add((0, 0, 0))

    directions = {
        'R': (1, 0, 0),  'L': (-1, 0, 0),
        'F': (0, 1, 0),  'B': (0, -1, 0),
        'U': (0, 0, 1),  'D': (0, 0, -1)
    }

    print("[*] Starting BFS...")

    while queue:
        cx, cy, cz, path = queue.popleft()
        current_type = get_cell_type(cx, cy, cz)
        
        if current_type == 3:
            print("\n[+] EXIT FOUND!")
            print(f"[+] Final Coordinates: ({cx}, {cy}, {cz})")
            print(f"[+] Path: {''.join(path)}")
            return

        for move_name, (dx, dy, dz) in directions.items():
            nx, ny, nz = cx + dx, cy + dy, cz + dz
            
            if (nx, ny, nz) not in visited:
                cell_type = get_cell_type(nx, ny, nz)
                if cell_type != 2:
                    visited.add((nx, ny, nz))
                    queue.append((nx, ny, nz, path + [move_name]))

    print("[-] No path found.")

if __name__ == '__main__':
    solve_maze()
```

<img width="671" height="172" alt="image" src="https://github.com/user-attachments/assets/2ab2286d-1c19-445b-955f-d42be0f224dc" />   

pipe the output of this into netcat service that you started   
```python
python3 solve.py | grep "Path:" | awk '{print $3}' | sed 's/./&\n/g' | nc <target_ip> <port> 
```

you will get the flag!!   
