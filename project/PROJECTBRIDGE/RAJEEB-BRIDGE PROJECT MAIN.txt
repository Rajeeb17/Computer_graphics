#include <iostream>
#include <GL/gl.h>
#include <GL/glut.h>
#include <windows.h>
#include <math.h>
using namespace std;

float trainX1 = -8.0;
float trainX2 = 10.0;
float carX1 = -6.0; // Position of Car 1
float carX2 = -9.5; // Position of Car 2
float trainSpeed1 = 0.1f;
float trainSpeed2 = 0.1f;
float carSpeed1 = 0.07f;
float carSpeed2 = 0.07f;
bool isPaused = false;

float cloudY = 3.5; // Base Y position for clouds
float cloudX1 = -6.0; // Initial X position for first cloud
float cloudX2 = 0.0;  // Initial X position for second cloud
float cloudX3 = 5.0;  // Initial X position for third cloud

float balloonX = -8.0; // Initial X position for hot air balloon
float balloonY = 2.0;  // Initial Y position for hot air balloon
bool isDragging = false;
float mouseX = 0.0f;
float mouseY = 0.0f;
int windowWidth = 1920;
int windowHeight = 1080;

float boatX1 = -10.0; // Initial X position for first boat
float boatX2 = -5.0;  // Initial X position for second boat

bool isDay = true; // Variable to track whether it's day or night

void initGL() {
    glClearColor(135 / 255.0, 206 / 255.0, 250 / 255.0, 1.0f); // Sky blue background
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(-10.0, 10.0, -5.0, 5.0);
    glMatrixMode(GL_MODELVIEW);
}
//22-49389-3sun
void drawSun() {
    glColor3ub(255, 255, 0);
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(8.5 + cos(angle) * 0.7, 3.5 + sin(angle) * 0.7);
    }
    glEnd();
}
//22-49389-3moon
void drawMoon() {
    glColor3ub(255, 255, 255);
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(-8.5 + cos(angle) * 0.5, 3.5 + sin(angle) * 0.5);
    }
    glEnd();
}
//22-49389-3cloud
void drawCloud(float x, float y) {
    if (isDay) {
        glColor3ub(255, 255, 255); // White color for clouds during the day
    } else {
        glColor3ub(50, 50, 50); // Ash black color for clouds during the night (dark gray)
    }

    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(x + cos(angle) * 0.8, y + sin(angle) * 0.5);
    }
    glEnd();
}

//22-49389-3trainwall
void trainWall() {
    glColor3ub(100, 100, 100);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, 0.81);
    glVertex2f(10.0, 0.81);
    glVertex2f(10.0, 0.0);
    glVertex2f(-10.0, 0.0);
    glEnd();
}

//22-49389-3roadwall1
void roadWall_1() {
    glColor3ub(150, 150, 150);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, 0.5);
    glVertex2f(10.0, 0.5);
    glVertex2f(10.0, 0.2);
    glVertex2f(-10.0, 0.2);
    glEnd();
}

//22-49389-3roadwall2
void roadWall_2() {
    glColor3ub(150, 150, 150);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, -0.5);
    glVertex2f(10.0, -0.5);
    glVertex2f(10.0, -0.7);
    glVertex2f(-10.0, -0.7);
    glEnd();
}

//22-49389-3raillineground
void rail_lineground() {
    glColor3ub(50, 50, 50);
    glBegin(GL_QUADS);
    glVertex2f(-10.0f, 0.82f);
    glVertex2f(10.0f, 0.82f);
    glVertex2f(10.0f, 0.40f);
    glVertex2f(-10.0f, 0.40f);
    glEnd();
}

//22-49389-3road
void road() {
    glColor3ub(50, 50, 50);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, 0.3);
    glVertex2f(10.0, 0.3);
    glVertex2f(10.0, -0.5);
    glVertex2f(-10.0, -0.5);
    glEnd();

    //road line across the road
    glColor3ub(255, 255, 255); // White color for the line
    glBegin(GL_LINES);
    glVertex2f(-10.0, -0.1); // Start of the line
    glVertex2f(10.0, -0.1);  // End of the line
    glEnd();
}

//22-49389-3bridgecolumn
void bridge() {
    /*glColor3ub(128, 128, 128);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, 0.82);
    glVertex2f(10.0, 0.82);
    glVertex2f(10.0, 1.2);
    glVertex2f(-10.0, 1.2);
    glEnd();*/

    glColor3ub(100, 100, 100);
    glBegin(GL_QUADS);
    glVertex2f(-9.0, -1.6);
    glVertex2f(-8.5, -1.6);
    glVertex2f(-8.5, 0.82);
    glVertex2f(-9.0, 0.82);
    glEnd();
}

//22-49389-3river
void river() {
    glColor3ub(70, 130, 180);
    glBegin(GL_QUADS);
    glVertex2f(-10.0, -1.60);
    glVertex2f(10.0, -1.60);
    glVertex2f(10.0, -5.0);
    glVertex2f(-10.0, -5.0);
    glEnd();

    // waves
    glBegin(GL_TRIANGLE_STRIP);
    for (float x = -10.0; x <= 10.0; x += 0.2) {
        float waveY = -1.65 + 0.1 * sin(3 * (x + glutGet(GLUT_ELAPSED_TIME) / 1000.0)); // Wave effect
        glColor3ub(70, 130, 180);
        glVertex2f(x, waveY);
        glVertex2f(x + 0.2, -1.60);
    }
    glEnd();
}

//22-49389-3streetlight
void drawStreetLight(float x, float y) {
    //  pole
    glColor3ub(105, 105, 105); // Gray color for the pole
    glBegin(GL_QUADS);
    glVertex2f(x - 0.05, y);
    glVertex2f(x + 0.05, y);
    glVertex2f(x + 0.05, y + 1.0);
    glVertex2f(x - 0.05, y + 1.0);
    glEnd();

    //the light
    glColor3ub(255, 255, 0); // Yellow color for the light
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(x + cos(angle) * 0.2, y + 1.0 + sin(angle) * 0.2);
    }
    glEnd();
}

//22-49389-3car
void drawCar(float x, float y, const char* color) {
    // Car body
    if (strcmp(color, "red") == 0) {
        glColor3ub(255, 0, 0); // Red color for car body
    } else {
        glColor3ub(0, 0, 255); // Blue color for car body
    }

    glBegin(GL_QUADS);
    glVertex2f(x, y);
    glVertex2f(x + 1.5, y);  // Reduced width
    glVertex2f(x + 1.5, y + 0.4); // Reduced height
    glVertex2f(x, y + 0.4);
    glEnd();

    // Car roof
    glColor3ub(50, 50, 50); // Black ash
    glBegin(GL_QUADS);
    glVertex2f(x + 0.3, y + 0.4); // Adjusted position
    glVertex2f(x + 1.2, y + 0.4); // Adjusted position
    glVertex2f(x + 1.2, y + 0.8); // Adjusted position
    glVertex2f(x + 0.3, y + 0.8); // Adjusted position
    glEnd();

    // Windows
    glColor3ub(173, 216, 230); // Light blue for windows
    glBegin(GL_QUADS);
    glVertex2f(x + 0.4, y + 0.5); // Adjusted position
    glVertex2f(x + 1.1, y + 0.5); // Adjusted position
    glVertex2f(x + 1.1, y + 0.7); // Adjusted position
    glVertex2f(x + 0.4, y + 0.7); // Adjusted position
    glEnd();

    // Wheels (now positioned below the road wall)
    glColor3ub(0, 0, 0); // Black for wheels
    float wheelRadius = 0.15;

    // Left wheel
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(x + 0.4 + cos(angle) * wheelRadius, y - 0.1 + sin(angle) * wheelRadius); // Half wheel behind
    }
    glEnd();

    // Right wheel
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(x + 1.1 + cos(angle) * wheelRadius, y - 0.1 + sin(angle) * wheelRadius); // Half wheel behind
    }
    glEnd();
}

//22-49389-3airballoon
void drawHotAirBalloon(float x, float y) {
    // Balloon envelope
    glColor3ub(255, 0, 255); // Pink color
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 10) {
        float angle = i * 3.14159 / 180;
        glVertex2f(x + cos(angle) * 0.5, y + sin(angle) * 0.75);
    }
    glEnd();

    // Balloon basket
    glColor3ub(139, 69, 19); // Brown color
    glBegin(GL_QUADS);
    glVertex2f(x - 0.2, y - 0.75);
    glVertex2f(x + 0.2, y - 0.75);
    glVertex2f(x + 0.2, y - 1.0);
    glVertex2f(x - 0.2, y - 1.0);
    glEnd();
}

//22-49389-3boat
void drawBoat(float x, float y) {
    // boat body
    glColor3ub(0, 0, 139); // Dark blue color
    glBegin(GL_QUADS);
    glVertex2f(x - 0.5, y);
    glVertex2f(x + 0.5, y);
    glVertex2f(x + 0.4, y - 0.2);
    glVertex2f(x - 0.4, y - 0.2);
    glEnd();

    // boat sail
    glColor3ub(255, 255, 255); // White color
    glBegin(GL_TRIANGLES);
    glVertex2f(x, y + 0.2);
    glVertex2f(x + 0.3, y);
    glVertex2f(x - 0.3, y);
    glEnd();
}

//22-49389-3train1
void drawTrain1() {
    // Locomotive body
    glColor3ub(255, 0, 0);
    glBegin(GL_QUADS);
    glVertex2f(trainX1, 1.0);
    glVertex2f(trainX1 + 1.5, 1.0);
    glVertex2f(trainX1 + 1.5, 0.6);
    glVertex2f(trainX1, 0.6);
    glEnd();

    // Locomotive(chimney)
    glColor3ub(50, 50, 50);
    glBegin(GL_QUADS);
    glVertex2f(trainX1 + 0.2, 1.0);
    glVertex2f(trainX1 + 0.4, 1.0);
    glVertex2f(trainX1 + 0.4, 1.2);
    glVertex2f(trainX1 + 0.2, 1.2);
    glEnd();

    // Front window with frame
    glColor3ub(50, 50, 50); // Frame
    glBegin(GL_QUADS);
    glVertex2f(trainX1 + 0.2, 0.9);
    glVertex2f(trainX1 + 1.3, 0.9);
    glVertex2f(trainX1 + 1.3, 0.7);
    glVertex2f(trainX1 + 0.2, 0.7);
    glEnd();

    glColor3ub(173, 216, 230); // Window
    glBegin(GL_QUADS);
    glVertex2f(trainX1 + 0.25, 0.85);
    glVertex2f(trainX1 + 1.25, 0.85);
    glVertex2f(trainX1 + 1.25, 0.75);
    glVertex2f(trainX1 + 0.25, 0.75);
    glEnd();

    // Wheels for locomotive
    float wheelPositions[] = {0.3f, 0.75f, 1.2f};
    for (int i = 0; i < 3; i++) {
        // Outer wheel
        glColor3ub(40, 40, 40);
        glBegin(GL_POLYGON);
        for (int j = 0; j < 360; j += 10) {
            float angle = j * 3.14159f / 180.0f;
            glVertex2f(trainX1 + wheelPositions[i] + cos(angle) * 0.15f,
                      0.45f + sin(angle) * 0.15f);
        }
        glEnd();

        // Inner wheel (hub)
        glColor3ub(100, 100, 100);
        glBegin(GL_POLYGON);
        for (int j = 0; j < 360; j += 10) {
            float angle = j * 3.14159f / 180.0f;
            glVertex2f(trainX1 + wheelPositions[i] + cos(angle) * 0.08f,
                      0.45f + sin(angle) * 0.08f);
        }
        glEnd();
    }

    // Additional passenger cars
    float carColors[][3] = {
        {255, 165, 0},  // Orange
        {0, 255, 0}     // Green
    };

    for (int i = 0; i < 2; i++) {
        float carOffset = 1.5f + (i * 1.5f);

        // Car body
        glColor3ub(carColors[i][0], carColors[i][1], carColors[i][2]);
        glBegin(GL_QUADS);
        glVertex2f(trainX1 + carOffset, 1.0);
        glVertex2f(trainX1 + carOffset + 1.5, 1.0);
        glVertex2f(trainX1 + carOffset + 1.5, 0.6);
        glVertex2f(trainX1 + carOffset, 0.6);
        glEnd();

        // Windows
        for (float w = 0.2f; w < 1.3f; w += 0.4f) {
            // Window frame
            glColor3ub(50, 50, 50);
            glBegin(GL_QUADS);
            glVertex2f(trainX1 + carOffset + w, 0.9);
            glVertex2f(trainX1 + carOffset + w + 0.3, 0.9);
            glVertex2f(trainX1 + carOffset + w + 0.3, 0.7);
            glVertex2f(trainX1 + carOffset + w, 0.7);
            glEnd();

            // Window glass
            glColor3ub(173, 216, 230);
            glBegin(GL_QUADS);
            glVertex2f(trainX1 + carOffset + w + 0.05, 0.85);
            glVertex2f(trainX1 + carOffset + w + 0.25, 0.85);
            glVertex2f(trainX1 + carOffset + w + 0.25, 0.75);
            glVertex2f(trainX1 + carOffset + w + 0.05, 0.75);
            glEnd();
        }

        // Wheels for each passenger car
        float carWheelPositions[] = {0.3f, 0.75f, 1.2f};
        for (int j = 0; j < 3; j++) {
            // Outer wheel
            glColor3ub(40, 40, 40);
            glBegin(GL_POLYGON);
            for (int k = 0; k < 360; k += 10) {
                float angle = k * 3.14159f / 180.0f;
                glVertex2f(trainX1 + carOffset + carWheelPositions[j] + cos(angle) * 0.15f,
                          0.45f + sin(angle) * 0.15f);
            }
            glEnd();

            // Inner wheel (hub)
            glColor3ub(100, 100, 100);
            glBegin(GL_POLYGON);
            for (int k = 0; k < 360; k += 10) {
                float angle = k * 3.14159f / 180.0f;
                glVertex2f(trainX1 + carOffset + carWheelPositions[j] + cos(angle) * 0.08f,
                          0.45f + sin(angle) * 0.08f);
            }
            glEnd();
        }
    }
}
//22-49389-3train2
void drawTrain2() {
    // Locomotive body
    glColor3ub(0, 0, 255); // Blue color for locomotive
    glBegin(GL_QUADS);
    glVertex2f(trainX2, 1.0);
    glVertex2f(trainX2 - 1.5, 1.0);
    glVertex2f(trainX2 - 1.5, 0.6);
    glVertex2f(trainX2, 0.6);
    glEnd();

    // Locomotive chimney
    glColor3ub(50, 50, 50);
    glBegin(GL_QUADS);
    glVertex2f(trainX2 - 0.2, 1.0);
    glVertex2f(trainX2 - 0.4, 1.0);
    glVertex2f(trainX2 - 0.4, 1.2);
    glVertex2f(trainX2 - 0.2, 1.2);
    glEnd();

    // Front window with frame
    glColor3ub(50, 50, 50); // Frame
    glBegin(GL_QUADS);
    glVertex2f(trainX2 - 0.2, 0.9);
    glVertex2f(trainX2 - 1.3, 0.9);
    glVertex2f(trainX2 - 1.3, 0.7);
    glVertex2f(trainX2 - 0.2, 0.7);
    glEnd();

    glColor3ub(173, 216, 230); // Window
    glBegin(GL_QUADS);
    glVertex2f(trainX2 - 0.25, 0.85);
    glVertex2f(trainX2 - 1.25, 0.85);
    glVertex2f(trainX2 - 1.25, 0.75);
    glVertex2f(trainX2 - 0.25, 0.75);
    glEnd();

    // Wheels for locomotive
    float wheelPositions[] = {-0.3f, -0.75f, -1.2f};
    for (int i = 0; i < 3; i++) {
        // Outer wheel
        glColor3ub(40, 40, 40);
        glBegin(GL_POLYGON);
        for (int j = 0; j < 360; j += 10) {
            float angle = j * 3.14159f / 180.0f;
            glVertex2f(trainX2 + wheelPositions[i] + cos(angle) * 0.15f,
                      0.45f + sin(angle) * 0.15f);
        }
        glEnd();

        // Inner wheel (hub)
        glColor3ub(100, 100, 100);
        glBegin(GL_POLYGON);
        for (int j = 0; j < 360; j += 10) {
            float angle = j * 3.14159f / 180.0f;
            glVertex2f(trainX2 + wheelPositions[i] + cos(angle) * 0.08f,
                      0.45f + sin(angle) * 0.08f);
        }
        glEnd();
    }

    // Additional passenger cars
    float carColors[][3] = {
        {255, 165, 0},  // Orange
        {0, 255, 0}     // Green
    };

    for (int i = 0; i < 2; i++) {
        float carOffset = -1.5f - (i * 1.5f);

        // Car body
        glColor3ub(carColors[i][0], carColors[i][1], carColors[i][2]);
        glBegin(GL_QUADS);
        glVertex2f(trainX2 + carOffset, 1.0);
        glVertex2f(trainX2 + carOffset - 1.5, 1.0);
        glVertex2f(trainX2 + carOffset - 1.5, 0.6);
        glVertex2f(trainX2 + carOffset, 0.6);
        glEnd();

        // Windows
        for (float w = -0.2f; w > -1.3f; w -= 0.4f) {
            // Window frame
            glColor3ub(50, 50, 50);
            glBegin(GL_QUADS);
            glVertex2f(trainX2 + carOffset + w, 0.9);
            glVertex2f(trainX2 + carOffset + w - 0.3, 0.9);
            glVertex2f(trainX2 + carOffset + w - 0.3, 0.7);
            glVertex2f(trainX2 + carOffset + w, 0.7);
            glEnd();

            // Window glass
            glColor3ub(173, 216, 230);
            glBegin(GL_QUADS);
            glVertex2f(trainX2 + carOffset + w - 0.05, 0.85);
            glVertex2f(trainX2 + carOffset + w - 0.25, 0.85);
            glVertex2f(trainX2 + carOffset + w - 0.25, 0.75);
            glVertex2f(trainX2 + carOffset + w - 0.05, 0.75);
            glEnd();
        }

        // Wheels for each car
        float wheelPositions[] = {-0.3f, -0.75f, -1.2f};
        for (int j = 0; j < 3; j++) {
            // Outer wheel
            glColor3ub(40, 40, 40);
            glBegin(GL_POLYGON);
            for (int k = 0; k < 360; k += 10) {
                float angle = k * 3.14159f / 180.0f;
                glVertex2f(trainX2 + carOffset + wheelPositions[j] + cos(angle) * 0.15f,
                          0.45f + sin(angle) * 0.15f);
            }
            glEnd();

            // Inner wheel (hub)
            glColor3ub(100, 100, 100);
            glBegin(GL_POLYGON);
            for (int k = 0; k < 360; k += 10) {
                float angle = k * 3.14159f / 180.0f;
                glVertex2f(trainX2 + carOffset + wheelPositions[j] + cos(angle) * 0.08f,
                          0.45f + sin(angle) * 0.08f);
            }
            glEnd();
        }
    }
}

//22-49389-3draw
void drawScene() {
    // Change background color based on time of day
    if (isDay) {
        glClearColor(135 / 255.0, 206 / 255.0, 250 / 255.0, 1.0f); // Day color
    } else {
        glClearColor(0.1, 0.1, 0.2, 1.0f); // Night color
    }

    glClear(GL_COLOR_BUFFER_BIT);
    glLoadIdentity();

    if (isDay) {
        drawSun();
    } else {
        drawMoon();
    }

    drawCloud(cloudX1, cloudY + sin(cloudX1) * 0.2); // //22-49389-3Cloud1
    drawCloud(cloudX2, cloudY + sin(cloudX2) * 0.2); // 22-49389-3Cloud2
    drawCloud(cloudX3, cloudY + sin(cloudX3) * 0.2); // 22-49389-3Cloud3
    drawHotAirBalloon(balloonX, balloonY + sin(balloonX) * 0.3); // 22-49389-3Hot Air Balloon
    drawBoat(boatX1, -1.5); // 22-49389-3Boat1
    drawBoat(boatX2, -1.5); // 22-49389-3Boat2
    bridge();
    river();
    rail_lineground();
    trainWall();
    drawTrain1();
    drawTrain2();

    // 22-49389-3street lights
    drawStreetLight(-8.0, 0.3);
    drawStreetLight(-4.0, 0.3);
    drawStreetLight(0.0, 0.3);
    drawStreetLight(4.0, 0.3);
    drawStreetLight(8.0, 0.3);
    road();
    roadWall_1();

    drawCar(carX1, 0.0, "red");// 22-49389-3car1
    drawCar(carX2, 0.0, "blue");//22-49389-3car2
    roadWall_2();

    glFlush();
}

void updateScene(int value) {
    if (!isPaused) {
        // 22-49389-3Train movement
        trainX1 -= trainSpeed1;
        trainX2 += trainSpeed2;
        if (trainX1 <-10) trainX1 = 10.0;
        if (trainX2 >10) trainX2 = -8.0;

        // 22-49389-3Car movement
        carX1 += carSpeed1;
        carX2 -= carSpeed2;
        if (carX1 > 10) carX1 = -10.0;
        if (carX2 < -10) carX2 = 10.0;

        //22-49389-3 Update cloud positions
        cloudX1 += 0.03;
        cloudX2 += 0.02;
        cloudX3 += 0.04;

        // Reset clouds if they move off screen
        if (cloudX1 > 10) cloudX1 = -10;
        if (cloudX2 > 10) cloudX2 = -10;
        if (cloudX3 > 10) cloudX3 = -10;

        // Only update balloon position if not being dragged
        if (!isDragging) {
            balloonX += 0.02;
            if (balloonX > 10) balloonX = -10;
        }

        // 22-49389-3Update boat positions
        boatX1 += 0.05;
        boatX2 += 0.07;
        if (boatX1 > 10) boatX1 = -10; // Reset boat 1 position
        if (boatX2 > 10) boatX2 = -10; // Reset boat 2 position
    }

    glutPostRedisplay();
    glutTimerFunc(50, updateScene, 0);  // Schedule the next update
}


void keyboard(unsigned char key, int x, int y) {
    const float speedIncrement = 0.01f;
    //22-49389-3day-night
    switch (key) {
        case 'y': case 'Y':
            isDay = true;
            break;
        case 'n': case 'N':
            isDay = false;
            break;

        // 22-49389-3T1 controls (left to right moving train)
        case 'q': case 'Q':  // Speed up Train 1
            trainSpeed1 += speedIncrement;
            break;
        case 'a': case 'A':  // Slow down Train 1
            trainSpeed1 = fmax(0.0f, trainSpeed1 - speedIncrement);
            break;

        // 22-49389-3T2 controls (right to left moving train)
        case 'w': case 'W':  // Speed up Train 2
            trainSpeed2 += speedIncrement;
            break;
        case 's': case 'S':  // Slow down Train 2
            trainSpeed2 = fmax(0.0f, trainSpeed2 - speedIncrement);
            break;

        // 22-49389-3C1 controls
        case 'e': case 'E':  // Speed up Car 1
            carSpeed1 += speedIncrement;
            break;
        case 'd': case 'D':  // Slow down Car 1
            carSpeed1 = fmax(0.0f, carSpeed1 - speedIncrement);
            break;

        // 22-49389-3C2 controls
        case 'r': case 'R':  // Speed up Car 2
            carSpeed2 += speedIncrement;
            break;
        case 'f': case 'F':  // Slow down Car 2
            carSpeed2 = fmax(0.0f, carSpeed2 - speedIncrement);
            break;

        // 22-49389-3Pause/Resume animation
        case ' ':  // Spacebar
            isPaused = !isPaused;
            break;

        // 22-49389-3Reset speeds to default
        case 'x': case 'X':
            trainSpeed1 = 0.05f;
            trainSpeed2 = 0.05f;
            carSpeed1 = 0.07f;
            carSpeed2 = 0.07f;
            break;
    }

    glutPostRedisplay();
}
// 22-49389-3Convert screen coordinates to world coordinates
void screenToWorld(int screenX, int screenY, float& worldX, float& worldY) {
    worldX = (screenX / (float)windowWidth) * 20.0f - 10.0f;
    worldY = (1.0f - screenY / (float)windowHeight) * 10.0f - 5.0f;
}

// Check if mouse is over the balloon
bool isOverBalloon(float x, float y) {
    // Check if point is within balloon's bounding box
    float balloonRadius = 2.5f; // Adjust based on balloon size
    return (x >= balloonX - balloonRadius && x <= balloonX + balloonRadius &&
            y >= balloonY - 1.0f && y <= balloonY + 0.75f);
}
//22-49389-3mouseballon
void mouse(int button, int state, int x, int y) {
    float worldX, worldY;
    screenToWorld(x, y, worldX, worldY);

    if (button == GLUT_LEFT_BUTTON) {
        if (state == GLUT_DOWN) {
            if (isOverBalloon(worldX, worldY)) {
                isDragging = true;
                mouseX = worldX - balloonX;
                mouseY = worldY - balloonY;
            }
        } else if (state == GLUT_UP) {
            isDragging = false;
        }
    }
}
//22-49389-3mouseballon
void motion(int x, int y) {
    if (isDragging) {
        float worldX, worldY;
        screenToWorld(x, y, worldX, worldY);

        // Update balloon position
        balloonX = worldX - mouseX;
        balloonY = worldY - mouseY;

        // Add bounds checking
        balloonX = fmax(-9.5f, fmin(9.5f, balloonX));
        balloonY = fmax(-4.0f, fmin(4.0f, balloonY));


    }
     glutPostRedisplay();
}
//22-49389-3mouseballon
void passiveMotion(int x, int y) {
    float worldX, worldY;
    screenToWorld(x, y, worldX, worldY);

    // Change cursor when over balloon
    if (isOverBalloon(worldX, worldY)) {
        glutSetCursor(GLUT_CURSOR_CROSSHAIR);
    } else {
        glutSetCursor(GLUT_CURSOR_INHERIT);
    }
}

int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutInitWindowSize(1920, 1080);
    glutCreateWindow("Project BRIDGE");
    initGL();
    glutDisplayFunc(drawScene);
    glutKeyboardFunc(keyboard);
    glutMouseFunc(mouse);           // Add mouse button callback
    glutMotionFunc(motion);         // Add mouse motion callback
    glutPassiveMotionFunc(passiveMotion); // Add passive motion callback
    glutTimerFunc(50, updateScene, 0);
    glutMainLoop();
    return 0;
}
