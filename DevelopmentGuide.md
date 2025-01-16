改めて開発手順を確認

## Pythonパッケージ開発環境作成
ros2 pkg create <packagename> --build-type ament_python --dependencies rclpy

## cppパッケージ開発環境作成
ros2 pkg create <packagename> --build-type ament_cmake --dependencies rclcpp

## 特定のパッケージだけのビルド

colcon build --packages-select <packagename>

## python実装のインストール

setup.pyの下記を記載

```
setup(
    name=package_name,
    version='0.0.0',
    packages=find_packages(exclude=['test']),
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='simo',
    maintainer_email='shimooku@gmail.com',
    description='TODO: Package description',
    license='TODO: License declaration',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            "py_node = my_py_pkg.my_first_node:main"
        ],
    },
)
```

## cpp実装

 * includeでsnakeラインが出てきたら、`ROS: Update C++ Properties`を実行して c_cpp_configuration.json をアップデートする
 
## 実行中のノードを調べる

ros2 node list

## 実行中のノードのインターフェースを調べる

% ros2 node info /py_test 

/py_test
  Subscribers:

  Publishers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /rosout: rcl_interfaces/msg/Log
  Service Servers:
    /py_test/describe_parameters: rcl_interfaces/srv/DescribeParameters
    /py_test/get_parameter_types: rcl_interfaces/srv/GetParameterTypes
    /py_test/get_parameters: rcl_interfaces/srv/GetParameters
    /py_test/list_parameters: rcl_interfaces/srv/ListParameters
    /py_test/set_parameters: rcl_interfaces/srv/SetParameters
    /py_test/set_parameters_atomically: rcl_interfaces/srv/SetParametersAtomically
  Service Clients:

  Action Servers:

  Action Clients:

## 同じノードを複数立ち上げるときは名前をリマップしないと通信時に区別ができなくなる

terminal#1 % ros2 run my_py_pkg py_node --ros-args -r __node:=-node1
terminal#2 % ros2 run my_py_pkg py_node --ros-args -r __node:=-node2
terminal#3 % ros2 run my_py_pkg py_node --ros-args -r __node:=-node3