---
title: Estimating π with the Monte Carlo Method
layout: post
outputs: [html, json]
categories: [mathematics, python, simulations]
date: 2024-11-09
math: true
---

Exploring Monte Carlo simulations has always intrigued me because of their real-world applications in areas like physics, finance, and artificial intelligence. And what better place to start than with estimating \\(π\\)? This seemingly abstract number, 3.14159..., holds a special place in mathematics and everyday life, and Monte Carlo simulations give us an intuitive, probability-based approach to approximate it. And here it is, the journey from the math to the code, with everything in between!

## The Core Idea

The essence of Monte Carlo simulation lies in the **law of large numbers**. This principle states that as we conduct more experiments, our results should converge toward the expected value. Here, our “experiment” is randomly placing points inside a square that contains a circle, then counting how many points fall within the circle versus those that lie outside it.

## Mathematical Foundation

Imagine a circle with radius \\(r\\) inscribed within a square. The square's side length is twice the radius, \\(2r\\), so its area becomes \\((2r)^2=4r^2\\). On the other hand, the area of the circle is \\(\pi r^2\\). Now, if we take the ratio of the areas, we get this:

$$
\frac{\text{Area of Circle}}{\text{Area of Square}} = \frac{\pi r^2}{4r^2} = \frac{\pi}{4}
$$

This ratio is crucial for our simulation. This relationship tells us that the probability of a random point landing inside the circle (as opposed to the square) is precisely \\(\frac{\pi}{4}\\). If we can simulate this process and find the proportion of the points inside the circle, we can estimate \\(π\\) by rearranging the formula a bit to get something like this:

$$
\pi \approx 4 \times \frac{\text{Points Inside Circle}}{\text{Total Points}}
$$

## Building the Simulation

Now let's dive into the Python implementation of this simulation. I've set up a simple step-by-step guide that we'll follow to estimate π using Monte Carlo methods.

1. **Set up the square and circle boundaries**:
    - Since our circle's radius is \\(r = 1\\), it will fit perfectly within a 2x2 square centered at the origin, between the intervals \\([-1, 1]\\) for both \\(x\\) and \\(y\\).
2. **Randomly generate points**:
    - Each point will have coordinates \\((x, y)\\) within the interal \\([-1, 1]\\), so it falls somewhere inside the square.
3. **Determine point placement**:
    - For each point, we check if it falls inside the circle by using the condition \\(x^2 + y^2 \leq 1\\).
4. **Calculate π**:
    - We count the points inside the circle and use the formula \\(\pi \approx 4 \times \frac{\text{Points Inside Circle}}{\text{Total Points}}\\) to estimate π.

With these steps in place, I started to code the simulation.

```python
import random
import matplotlib.pyplot as plt

# Constants

# Number of random points to generate
NUM_POINTS = 1000

# Radius of the circle
RADIUS = 1

# Lists to store points

# Points inside the circle
inside_x = []
inside_y = []

# Points outside the circle
outside_x = []
outside_y = []

# Counter for points inside the circle
inside_circle = 0

for _ in range(NUM_POINTS):
    # Generate random x and y coordinates
    x = random.uniform(-RADIUS, RADIUS)
    y = random.uniform(-RADIUS, RADIUS)

    # Check if the point is inside the circle
    if x ** 2 + y ** 2 <= RADIUS ** 2:
        inside_circle += 1
        inside_x.append(x)
        inside_y.append(y)
    
    else:
        outside_x.append(x)
        outside_y.append(y)

# Estimate pi using Monte Carlo method
pi_estimate = (inside_circle / NUM_POINTS) * 4
print(f"Estimated PI: {pi_estimate}")

# Visualizing the results
plt.figure(figsize=(6, 6))
plt.scatter(inside_x, inside_y, color='blue', s=1, label='Inside Circle')
plt.scatter(outside_x, outside_y, color='red', s=1, label='Outside Circle')
circle_outline = plt.Circle((0, 0), RADIUS, color='black', fill=False, linewidth=1.5)
plt.gca().add_artist(circle_outline)
plt.gca().set_aspect('equal', adjustable='box')
plt.legend()
plt.title(f"Monte Carlo Simulation of PI (Estimate: {pi_estimate})")
plt.xlabel('x')
plt.ylabel('y')

plt.savefig("monte_carlo_pi_estimate.png")
print("Plot saved as 'monte_carlo_pi_estimate.png'")
```

### Visualizing the Simulation

To make the simulation more interactive, I visualized the points using a Python library called `matplotlib`. The library is versatile and allows us to plot the points inside and outside the circle, along with the circle itself. The resulting plot gives a visual representation of the simulation, making it easier to understand the concept.

## Challenges in Accuracy vs. Precision

Monte Carlo methods come with unique characteristics in terms of **accuracy** and **precision**—concepts that are often confused but critically important for understanding the quality of our π estimate. Let’s break down what these mean in the context of our simulation.

### Accuracy

When we talk about **accuracy**, we’re referring to how close our estimated value of \\(π\\) is to the actual value, 3.14159…. In a Monte Carlo simulation, accuracy improves as we increase the number of points, but because the method relies on random sampling, our estimate is always an approximation rather than an exact answer. For this reason, the Monte Carlo method may not yield exact accuracy but can come impressively close, especially with large sample sizes.

### Precision

**Precision** describes the consistency of our results. For instance, if we run multiple simulations, we might see values like 3.14, 3.15, and 3.13, which are reasonably close to each other and suggest high precision, even if the results aren’t exactly 3.14159. Precision can be influenced by randomness and the uniformity of our sample distribution. With more points, the precision improves, and each simulation’s result is likely to cluster more tightly around the actual value of \\(π\\).

To visualize the balance between accuracy and precision, consider a dartboard. High accuracy means hitting close to the bullseye, while high precision means grouping darts tightly together, even if they’re not near the bullseye. In our \\(π\\) estimation, adding more points helps both accuracy and precision, as each dart (or point) collectively brings us closer to the true value of \\(π\\).

### Balancing Accuracy and Efficiency

One of the challenges I've faced while working with the Monte Carlo simulations, especially for estimating \\(π\\), is finding the right balance between **accuracy** and **efficiency**. With limited computational resources, achieving an accurate estimate without excessively increasing the number of points is ideal for the situation. Here's how we typically approach this balance:

1. **Early Stopping**: In some applications, we might monitor our π estimate as points accumulate. When we observe that additional points only cause minor adjustments to the estimate, we might “stop early” to save on processing time.
2. **Batch Sampling**: Instead of adding points one-by-one, we can increase accuracy by adding large batches of points and recalculating the estimate. This approach can sometimes reduce the “noise” of randomness seen with smaller sample sizes, yielding a more stable estimate sooner.
3. **Variance Reduction Techniques**: Techniques like stratified sampling (choosing points in a more structured way) or antithetic variates (pairing points to offset randomness) can reduce the variance, thereby improving both accuracy and efficiency without requiring a massive number of points.

## Why Does Monte Carlo Works?

The Monte Carlo simulation is widely loved in fields beyond just mathematics—finance, physics, and artificial intelligence, to name a few. But why does it work so well?

1. **Scalability**: Monte Carlo methods can be applied to complex scenarios where an exact solution is difficult or even impossible to find. Simulations can be scaled up easily by simply increasing the number of random points.
2. **Simplicity**: While certain Monte Carlo implementations can get sophisticated, our π estimation example is straightforward and demonstrates how a simple setup can yield meaningful results. This simplicity is a large part of what makes Monte Carlo methods accessible for a wide range of problem-solving.
3. **Adaptability**: Monte Carlo methods allow for flexibility in approach. Whether we’re estimating π, calculating integrals, or simulating physical systems, these techniques can adapt to different applications with ease.

And that's pretty much a wrap on our Monte Carlo journey to estimate \\(π\\)! Had a lot of fun exploring this topic, and I hope you enjoyed it too. Until next time, happy coding!