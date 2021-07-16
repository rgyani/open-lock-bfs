# open-lock-bfs

You have a lock in front of you with 4 circular wheels. Each wheel has 10 slots: '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'. The wheels can rotate freely and wrap around: for example we can turn '9' to be '0', or '0' to be '9'. Each move consists of turning one wheel one slot.

The lock initially starts at '0000', a string representing the state of the 4 wheels.

You are given a list of dead ends, meaning if the lock displays any of these codes, the wheels of the lock will stop turning and you will be unable to open it.

Given a target representing the value of the wheels that will unlock the lock, return the minimum total number of turns required to open the lock, or -1 if it is impossible.


```
Example 1:

Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6

Explanation:
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".
```

```
Example 2:

Input: deadends = ["8888"], target = "0009"
Output: 1

Explanation:
We can turn the last wheel in reverse to move from "0000" -> "0009".
```

```
Example 3:

Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1

Explanation:
We can't reach the target without getting stuck.
```


### Solution

We can visualize the steps to the above problem as walking down a tree, such that from each node (say '0000'),
we go to all the **8** nodes represented by the possible 'turns' eg.

'0000' -> '1000', '9000', '01000', '0900', '0010', '0090', '0001', '0009'

and so on


Now all we need to do is, to find the shortest path from the input node '0000' to the result node, say '0202' 

For this we can use Breadth-First Search to find the shortest path, while avoiding the deadends


Now, for the actual code, we can either represent each node by a string eg "0000", "0010" etc. 
but since we need to increment and decrement the node values to find the next node, we will instead use integers 

For each node, we can find all the children, by considering the diagram below

Node: 2345

|  p    | c = Node / p  | v = c % 10 |
|-------|----|---|
|  1    | 2345  | 5 | 
| 10    | 234   | 4 |
| 100   | 23    | 3 |
| 1000  | 2     | 2 |


### Code

```
    private static int[] P10= {1, 10, 100, 1000};
    private static int[] FWD_BWD= {1, 9};

    public int openLock(String[] deadends, String target) {
        // Create an bool array to store all available positions
        boolean[] seen= new boolean[10000];
        
        // Store the deadends in this array, so we dont visit these
        for(int i=0; i<deadends.length; i++) 
            seen[Integer.parseInt(deadends[i])]= true;
            
        // If 0000 itself is a deadend, exit    
        if(seen[0]) return -1;
        
        // Sanity check
        int tIdx= Integer.parseInt(target);
        if(tIdx==0) return 0;
        
        // Store a queue for BFS
        Queue<Integer> queue= new ArrayDeque<>();
        
        // We start from 0000, so mark it as seen, so we dont revisit it
        seen[0]= true;
        queue.add(0);
        
        // BFS, we keep pushing new elements to the queue and iterate over it
        for(int level= 0; !queue.isEmpty(); level++){
            int k= queue.size();
            // for each code attempt 8 transitions, check for 1. target 2. visited/dead
            while(k-->0)
                for(int idx= queue.poll(), j=0; j<4; j++)
                
                    // Roll forward or backward
                    for(int fwdBwd:FWD_BWD){
                    
                        // Find which digit we want to increment
                        int digit= idx/P10[j]%10;
                        
                        // Calculate next
                        int next= idx+((digit+fwdBwd)%10-digit)*P10[j];
                        
                        // Element found
                        if(next==tIdx) return level+1;
                        
                        // Already visited this element (or deadend)
                        if(seen[next]) continue;
                        
                        // Mark this element as seen
                        seen[next]= true;
                        
                        // Push next to the queue 
                        queue.add(next);
                    }
        }
        return -1;
    }
```