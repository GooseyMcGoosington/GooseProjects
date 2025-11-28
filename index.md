

# Software Rendering with Portals
## Introduction
Software Rendering is a concept that has been practiced since the early 1970s. It utilizes the CPU (central processing unit) instead of the GPU (graphics processing unit) and is associated with games such as Elite (1984), DOOM (1993), and Quake (1994). Such games are quite simple for performance, like in the case of DOOM which would apply affine texture mapping and restrict camera movement to 2 axes. It is a somewhat maths heavy topic that requires someone to know Linear Algebra (Matrices), Euclidean Geometry (Line-line intersection), and critical thinking. In this tutorial, you will be able to have a very basic understanding of what these mean.

## Matrices and Vectors
Matrices can be thought of rectangular arrays of numbers arranged in rows and columns. They are heavily linked to Vectors and are used in manipulating them. A Vector would be a single matrix which has direction and magnitude. These are what make up 3D geometry and how they are shifted as the perspective changes. A vertex is a single point.

For example, let's say we have two vectors and we want to work out the distance.
$$
A = \left[\begin{array}{c}
12 \\[4pt] 7 \\[4pt] 5
\end{array}\right], \quad
B = \left[\begin{array}{c}
-7 \\[4pt] 3 \\[4pt] 8
\end{array}\right]
\qquad
\|B - A\| = \sqrt{(-7 - 12)^2 + (3 - 7)^2 + (8 - 5)^2} \approx 19.65
$$

This is the Pythagorean distance in 3D. For the 2D XY rotation:

$$
\text{yaw} = 35.00^\circ
\\[10pt]
\left[\begin{array}{c}
rx \\[4pt] ry
\end{array}\right]
=
\left[\begin{array}{c}
x_1 - x_0 \\[4pt] y_1 - y_0
\end{array}\right]
\\[20pt]
\left[\begin{array}{c}
tx \\[4pt] ty
\end{array}\right]
=
\left[\begin{array}{cc}
\cos(\text{yaw}) & -\sin(\text{yaw}) \\[4pt]
\sin(\text{yaw}) & \cos(\text{yaw})
\end{array}\right]
\left[\begin{array}{c}
rx \\[4pt] ry
\end{array}\right]
$$


What this does is compute the relative distance from a vertex to the camera on a 2D plane and then rotates it around the camera by yaw.
## Getting Setup
Getting setup is a relatively easy task but requires you to understand a programming language such as Python or C. As C is a simple compiled language, and much faster as such, this will be the language I am going to use. Use your desired editor like Visual Studio Code. Install SDL2 and then include it into a file called main.c - drop it into your project folder or install it onto your system. You can copy this example into your main.c and compile it to get a 16 bit software rendered window working.

```c
#include <SDL2/SDL.h>
#include <stdbool.h>

SDL_Window *WINDOW = NULL;
SDL_Surface *FRAMEBUFFER = NULL;
SDL_Surface *WINDOW_SURFACE = NULL;
Uint16 *FRAMEBUFFER_PIXELS = NULL;
SDL_Rect destRect = {0, 0, 1920, 1080};

int main(int argc, char* argv[]) {
    WINDOW = SDL_CreateWindow(
        "Portal Renderer",
        SDL_WINDOWPOS_CENTERED,
        SDL_WINDOWPOS_CENTERED,
        1920, 1080,
        SDL_WINDOW_SHOWN
    );

    WINDOW_SURFACE = SDL_GetWindowSurface(WINDOW);

    FRAMEBUFFER = SDL_CreateRGBSurface(
        0, 1920, 1080, 16,
        0xF800, 0x07E0, 0x001F, 0
    );

    FRAMEBUFFER_PIXELS = (Uint16*)FRAMEBUFFER->pixels;

    bool quit = false;

    while (!quit) {
        SDL_Event event;
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) {
                quit = true;
            }
        }

        SDL_BlitScaled(FRAMEBUFFER, NULL, WINDOW_SURFACE, &destRect);
        SDL_UpdateWindowSurface(WINDOW);
    }

    SDL_DestroyWindow(WINDOW);
    SDL_Quit();

    return 0;
}
}
```
