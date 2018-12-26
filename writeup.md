## Building a State Estimator

### Determine standard deviation

To determine the standard deviation of GPS and accelerometer data I used pandas as seen in `notebooks/std.ipynb`. For GPS.X I got around 0.7 and for IMU.AX around 0.5.

### Improve gyro integration

For this instead of using the already implemented linear approach to do the integration I used the Quaternion class as suggested to do the body rate integration (based on Euler angles).

### Prediction Step

#### Predict state

The first part of this step is to predict the state using a dead-reckoning as described in the lecture. The `PredictState` method takes the acceleration and the body rates as input and does an integration forward in time using `dt`. This is based on eq. 40 in [1]. To get the rotation matrix needed for the attitude state prediction I used the handy method called `Rotate_BtoI` of class Quaternion.

This is the state mean part of the EKF predict step.

#### Predict covariance

The next step is to account for the state covarince according to the EKF predict state as described in the lectures as well as [1]. To do so we first need to implement `GetRbgPrime` which creates the derivative for the rotation matrix as described in eq. 52 in [1].
Using this derivative we can create the jacobian of our state transition function `g` as in eq. 51 in [1]. Using the jacobian we use the formular described in the lectures and [1] (algorithm 2) to calculate the covariance (`ekfCov`).

### Magnometer Update

The first task was to tune the `QYawStd` config, which I ended up setting to `.8`.

The magnometer update can be found in [1] under 7.3.2. The magnometer reports a new value for yaw in the global frame [1]. The implementationwas failry straight forwars, the only thing not to forget is the normalization in range [-pi, pi].

### GPS Update

The GPS Update can be found in cahpter 7.3.1 in [1]. Using eq. (53, 54, 55) from [1] the update from a GPS measurement is failry stright forward as well.

### Using own PID Controller

I copied the code and config from the last project (https://github.com/magnusja/FCND-Controls-CPP). My controller failed miserably with the estimated state. So I tuned it a bit and the quadrotor is able to fly the square reasonbly well and also meets the specs for scenario 11 (GPS Update).

### TODO

* Tune controller parameters again (drone still looks shaky sometimes)
* Implement UKF :)

#### References
[1] Estimation for Quadrotors (https://www.overleaf.com/read/vymfngphcccj#/54894644/)
