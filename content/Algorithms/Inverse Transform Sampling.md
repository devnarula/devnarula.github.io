---
title: Inverse Transform Sampling
draft: false
---
The motivation for this blog was a leetcode problem ([Generate Random Point in a Circle](https://leetcode.com/problems/generate-random-point-in-a-circle/description/)) I was solving recently. Since this problem was labelled as medium difficulty, I did not have much expectations going into it.

The problem statement is simply asking to generate a random point inside a circle of given radius and center that follows uniform distribution.(Doesn't seem that bad?)

I knew that C++ allows you to generate a random double in the range $[0,1]$ that follows uniform distribution. Let's call this distribution $U$. 

Now transforming this distribution to follow the constraints of a given circle felt like a challenge.

Afer some thought, I was struggling to obtain a simple algebraic transformation of $U$ to fit under the constraints of a circle. I could scale the points to fit within a square with the center $(x_0,y_0)$ and keep generating a random point until it fits in the circle.

The probability of a random point generated using this methodology to not fit within a circle:

 $$ 
 P(\text{point in circle of radius r}) = \frac{\pi r^2}{(2r)^2} = \frac{\pi}{4} \approx 0.785
 $$ 

Since, the probability of success of a point follows a geometric distribution, we can determine the expected number of trials:

$$ 
E(X) = \sum_{n = 0}^{\infty} n\cdot p(1-p)^{n - 1} = 1 + (1-p) + (1-p)^2 + ... = \frac{1}{p}
$$

Hence, the expected number of guesses in our case:
$$ 
E(X) = \frac{1}{\frac{\pi}{4}} \approx 1.273 
$$

So it will take us about 1-2 tries on average to generate a point which first the bounds of our circle. I could use this as a possible solution to the problem and but the worst case was still technically infinite time! There is a chance of our first $10^5$ guesses to be incorrect in a row (although the probability of that event is negligible).

I tried to play around with the points generated and different transformations but it lead me to no success. It had already been longer than I have spent on any other leetcode medium problem and I was ready to give up. 

Fortunately, that's when I remember the existence of polar coordinates!

All of my computations have been in the cartesian coordinate system but I realized the parameters to a polar coordinate fit the requirements for this problem quite well. The only two parameters required are the radius and the angle the coordinate makes with the X-axis.

Therefore, I just need two uniform distributions, $R$ and $\theta$ such that:

$$
r \sim R \in [0,r_0], \theta \sim \mathcal{\theta} \in [0,2\pi]
$$

From here, the solution was quite simple. I simply had to generate a radius and an angle in the given bounds and convert the polar coordinates to cartesian. A simple 3 line python solution and I could see the light at the end of the tunnel!

```python
class Solution:

    def __init__(self, radius: float, x_center: float, y_center: float):
        self.radius = radius
        self.x = x_center
        self.y = y_center
    def randPoint(self) -> List[float]:
        r = random.random()
        theta = random.random()*2*math.pi
        return [self.x+r*math.cos(theta),self.y+r*math.sin(theta)]
```