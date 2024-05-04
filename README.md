# LIO-SLAM-build-error

## Boost Linking

LIO 종류의 SLAM을 빌드할 때 cannot find -lBoost ~ 이런식의 에러가 발생할 때가 있다.

(필자의 경우 FAST_LIO_LC을 빌드)

이때 PGO의 CMakeList에서 Boost Library를 Linking 해주는 작업이 필요하다.

    set(Boost_USE_STATIC_LIBS OFF)
    set(Boost_USE_MULTITHREADED ON)
    set(Boost_USE_STATIC_RUNTIME OFF)

    find_package(Boost COMPONENTS 
    serialization 
    timer 
    thread 
    chrono
    )

위의 코드를 추가해주고 include_directories와 target_link_libraries(alaserPGO~)에 ${Boost_INCLUDE_DIRS}을 추가해주면 간단히 해결된다.

    include_directories(
    include
    ${catkin_INCLUDE_DIRS} 
    ${PCL_INCLUDE_DIRS}
    ${CERES_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
    ${GTSAM_INCLUDE_DIR}
    ${Boost_INCLUDE_DIRS}
    )

    target_link_libraries(alaserPGO 
    ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${CERES_LIBRARIES}
    ${OpenMP_CXX_FLAGS} ${Boost_INCLUDE_DIRS}
    gtsam
    )

## error: ‘Identity’ is not a member of ‘gtsam::Pose3’

gtsam을 사용하는 LIO 슬램을 빌드할 때 이런 에러가 생길 수 있다.

gtsam의 버전과 관련되 문제로 상위 버전에서의 gtsam에서는 대문자로 시작하는 Identity()를 사용하지만 하위 버전의 gtsam에서는 소문자로 시작하는 identity()로 표기하기 때문이다.

사용하고 있는 gtsam의 버전에 따라 코드에서 대문자를 소문자로, 소문자를 대문자로 바꿔 작성하면 간단히 해결된다. 

## ceres solver build error

ceres solver가 상위 버전으로 업데이트되며 바뀐 부분이 있다.

ceres::LocalParameterization

-> ceres::Manifold

ceres::EigenQuaternionParameterization()

-> ceres::EigenQuaternionManifold()

으로 바꿔주면 해결된다.

## fatal error: fast_lio/Pose6D.h: No such file or directory

1. CmakeList의 find_package에서 맨 뒤에 genmsg 추가
   
find_package(catkin REQUIRED COMPONENTS geometry_msgs nav_msgs sensor_msgs roscpp rospy std_msgs pcl_ros tf livox_ros_driver message_generation eigen_conversions genmsg )


2. add_dependencies(fastlio_mapping fast_lio_generate_messages_cpp) 추가
