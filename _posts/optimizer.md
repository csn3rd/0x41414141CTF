# Challenge: optimizer

`US instance: 45.134.3.200 9660`

# Solution
Let's connect to the server and see what happens.

```
you will be given a number of problems give me the least number of moves to solve them
level 1: tower of hanoi
```

So seems like we need to figure out how many moves it takes to solve the tower of hanoi. According to [Wikipedia](https://en.wikipedia.org/wiki/Tower_of_Hanoi), the puzzle can be solved in 2<sup>*n*</sup>-1 moves if there are n disks.

All we need to do is write code to read in the line of disks and then send that answer.

After solving it once, it gives us another input. After solving 25 different tower of hanoi puzzles, we now have reached level 2.

```
level 2 : merge sort, give me the count of inversions
```

We can use the code in this [Stack Overflow answer](https://stackoverflow.com/a/15151050)

After calculating the number of inversions for 25 different arrays, we get the flag.

Python Code:
```
from pwn import *

r = remote('45.134.3.200', 9660)
print(r.recvline())
print(r.recvline())

# level 1: tower of hanoi
for i in range(25):
	print(i+1)
	problem = r.recvline().decode("utf-8")
	# print(problem)
	ans = str(2**(problem.count(',')+1)-1)
	r.send(ans)
	# print(ans)


# level 2: merge sort inversions
def count_inversion(lst):
    return merge_count_inversion(lst)[1]

def merge_count_inversion(lst):
    if len(lst) <= 1:
        return lst, 0
    middle = int( len(lst) / 2 )
    left, a = merge_count_inversion(lst[:middle])
    right, b = merge_count_inversion(lst[middle:])
    result, c = merge_count_split_inversion(left, right)
    return result, (a + b + c)

def merge_count_split_inversion(left, right):
    result = []
    count = 0
    i, j = 0, 0
    left_len = len(left)
    while i < left_len and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            count += left_len - i
            j += 1
    result += left[i:]
    result += right[j:]
    return result, count        



print(r.recvline())
for i in range(25):
	print(i+1)
	problem = (r.recvline().decode("utf-8"))
	replace = [' ', '\t', '\n', '[', ']', '>']
	for s in replace:
		problem = problem.replace(s, '')
	# print(problem)
	array = [int(x) for x in problem.split(',')]
	ans = str(count_inversion(array))
	r.send(ans)
	# print(ans)

print(r.recvline())
```

# Flag
`you won here's your flag flag{g077a_0pt1m1ze_3m_@ll}`
