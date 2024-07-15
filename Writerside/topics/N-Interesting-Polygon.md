# N-Interesting Polygon

<resource src="N-Interesting Polygon.pptx"></resource>

![n-interesting-polygon-01.png](n-interesting-polygon-01.png)

```Python
#         *
#    *   ***
# * *** *****
#    *   ***
#         *
# n - interesting polygon

def solution(n):
    # basic case  - return 1
    if n == 1:
        return 1
    area = 1
    # iterative accumulation
    # each next polygon will add 4*(n-1) squares to the previous polygon
    # range (2, n+1) - start with level 2 until level n, n+1 because range is not end-inclusive
    for i in range(2, n + 1):
        area = area + 4 * (i - 1)
    return area


for i in range(1, 6):
    print(solution(i))
```