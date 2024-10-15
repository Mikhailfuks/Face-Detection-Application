#include <opencv2/opencv.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main() {
    // Load the Haar cascade for face detection
    CascadeClassifier faceCascade;
    if (!faceCascade.load("haarcascade_frontalface_default.xml")) {
        cerr << "Error loading face cascade file!" << endl;
        return -1;
    }

    // Open the default camera
    VideoCapture cap(0);
    if (!cap.isOpened()) {
        cerr << "Error opening video capture!" << endl;
        return -1;
    }

    // Main loop for processing video frames
    while (true) {
        Mat frame;
        cap >> frame; // Capture a frame from the camera

        if (frame.empty()) {
            break;
        }

        // Convert the frame to grayscale
        Mat grayFrame;
        cvtColor(frame, grayFrame, COLOR_BGR2GRAY);

        // Detect faces
        vector<Rect> faces;
        faceCascade.detectMultiScale(grayFrame, faces, 1.1, 3, 0, Size(30, 30));

        // Draw rectangles around detected faces
        for (size_t i = 0; i < faces.size(); i++) {
            Point center(faces[i].x + faces[i].width / 2, faces[i].y + faces[i].height / 2);
            ellipse(frame, center, Size(faces[i].width / 2, faces[i].height / 2), 0, 0, 360, Scalar(0, 255, 0), 4, 8, 0);
        }

        // Display the result
        imshow("Face Detection", frame);

        // Break the loop if 'q' is pressed
        if (waitKey(30) == 'q') {
            break;
        }
    }

    // Release resources
    cap.release();
    destroyAllWindows();

    return 0;
}
