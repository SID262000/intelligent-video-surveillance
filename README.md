# Intelligent Video Surveillance

A small collection of Python scripts and a Flask frontend for basic
intelligent video surveillance tasks including object/surveillance
monitoring, fall detection and social-distancing detection using a
YOLO-based person detector.

This repository contains example code and simple demos intended for
research, prototyping and educational use. It demonstrates how OpenCV
and YOLO can be used to detect people, measure distances between
people (social distancing), and detect falls or unattended objects.

Contents
--------
- `app.py` - A Flask web application with several routes that serve
	simple pages and a surveillance demo which performs unattended object
	detection and (optionally) email alerts.
- `detection.py` - Utility function `detect_people()` that runs a
	YOLO-based forward pass and returns person detections (used by the
	social distancing script).
- `fall_detection.py` - Simple OpenCV-based fall detection demo using
	background subtraction and aspect-ratio heuristic.
- `social_distance_detector.py` - Script that uses `detection.detect_people`
	with YOLO to compute pairwise distances and flag social distancing
	violations in video frames.
- `social_distancing_config.py` - Configuration constants for the
	social distancing detector (model path, thresholds, GPU toggle,
	minimum pixel distance).

Requirements
------------
- Python 3.8+ (recommendation)
- OpenCV (cv2)
- numpy
- imutils
- scipy
- flask
- flask-mail
- flask-mysqldb

You can install the common dependencies with pip (inside a virtualenv):

```bash
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install opencv-python numpy imutils scipy flask flask-mail flask-mysqldb
```

Note: Depending on your platform you may prefer `opencv-contrib-python`.

YOLO model files
----------------
The social distancing script expects a YOLO model directory named
`yolo-coco` (configurable in `social_distancing_config.py`). The folder
should contain:

- `coco.names`  (COCO class names)
- `yolov3.cfg`  (YOLOv3 configuration)
- `yolov3.weights` (pre-trained weights)

You can download these from the official Darknet / YOLO resources:

- `yolov3.weights`: https://pjreddie.com/media/files/yolov3.weights
- `yolov3.cfg` and `coco.names` from the Darknet repo or other
	YOLO sources.

Usage
-----
1) Social distancing detector

```bash
# Run against a video (or leave out --input to use your webcam)
python social_distance_detector.py --input pedestrians.mp4 --output output.avi --display 1
```

The script will load YOLO from `yolo-coco`, detect people, compute
pairwise Euclidean distances between detections' centroids, and mark
pairs that are closer than `MIN_DISTANCE` in
`social_distancing_config.py`.

2) Fall detection

```bash
python fall_detection.py
```

This simple demo reads `fall.mp4` (change the filename in the script if
needed) and uses background subtraction plus a basic aspect-ratio
heuristic to decide whether a fall-like pose was observed.

3) Flask surveillance app

```bash
python app.py
```

The Flask app exposes several routes (see `app.py`):

- `/` — login page (renders `login.html`)
- `/index` — main dashboard page
- `/Survilliance` — runs a simple unattended-object surveillance demo
	(uses OpenCV to read `video1.avi`) and — if an unattended object is
	detected — sends an email alert using the configured SMTP settings.

Important: `app.py` currently contains hard-coded MySQL and email
credentials. For security and flexibility, replace these with
environment variables or a secure configuration before deploying. The
app expects a MySQL database for user authentication if you use the
login flow.

Configuration notes & tips
-------------------------
- `social_distancing_config.py` contains `MODEL_PATH`, `MIN_CONF`,
	`NMS_THRESH`, `USE_GPU` and `MIN_DISTANCE` — tweak these values to
	suit your video scale and camera setup.
- GPU acceleration (CUDA) can be enabled by setting `USE_GPU = True`
	and ensuring your OpenCV build supports CUDA.
- The pixel-based `MIN_DISTANCE` is not a real-world measure and
	depends on camera perspective. For accurate distancing use
	homography / calibration to map pixels to meters.

Security & privacy
------------------
- This code is for local experimentation. If deploying for production,
	remove hard-coded credentials, enable secure authentication, and
	comply with local privacy laws when recording or processing video.

Contributing
------------
Contributions and improvements are welcome. Good next steps:

- Add a `requirements.txt` or `pyproject.toml` for reproducible
	installs.
- Replace hard-coded secrets in `app.py` with environment variables.
- Add better fall detection ML models or calibration for distance
	measurement.

License
-------
This project does not include an explicit license file. If you plan
to share or publish the project, add a `LICENSE` to clarify reuse
terms.

Contact
-------
If you have questions or want to collaborate, open an issue or contact
the repository owner.
