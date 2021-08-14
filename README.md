

# 1 MoveIt!を使ったマニュピレータの制御
## 1.1 ROS/MoveIt!によるマニピュレータの制御   
 

６自由度のアームを表示させる

最初に以下のコマンドを入力する

```bash
$ roslaunch sixdofarm sixdofarm.launch
  
```
次に別の端末で以下のコマンドを入力

```bash
  
$ rosrun rviz rviz
```
RVizが立ち上がるので
設定を変更してアームを表示させる

まず、画面左の方にある"Fixed FrAME"を"map"→"base"に
次に左下付近にある"Add"をクリックして"RobotModel"を追加する

アームが表示され、スライドバーで動かすことができる



### MoveIt!を利用した自作マニピュレータの制御  

以下のコマンドを入力する
```
$ roslaunch sixdofarm_moveit_config demo.launch 
```
RVizにアームが表示され、アームの先端にあるボールのようなものをドラッグすると姿勢を変更できる。
また、"Planning"のタブを選び，"Commands"の下にある"Plan"というボタンを押すとその姿勢への計画をしてくれ、
"Execute"ボタンを押すと移動を行う。


  <br>

## 1.2 Gazeboによるマニピュレータのシミュレーション  
### Gazebo上でのマニピュレータの動力学シミュレーション  

１つ目の端末で以下のコマンドを入力するとGazeboが立ち上がる
```
$ roslaunch sixdofarm_gazebo sixdofarm.launch
```
次に２つ目の端末で以下のコマンドを入力するとコントローラーが立ち上がる

```
$ roslaunch sixdofarm_control sixdofarm_control.launch
```
最後に３つ目の端末で以下のコマンドを入力するとで指定した角度（今回は1.5rad）に動く
```
$ rostopic pub -1 /sixdofarm/joint2_position_controller/command std_msgs/Float64 "data: 1.5"
```

  <br>


また、下記コマンドを入力するとMoveIt!とGazeboが立ち上がる  
```
$ roslaunch sixdofarm_moveit_config sixdofarm_moveit.launch
```
  <br>

##　2 ROSを使った移動ロボットの制御  
### 2.1 Gazebo上での移動ロボットモデルの構築    


１つ目の端末で以下のコマンドを入力するとGazeboとRvizが立ち上がる．
```
$ roslaunch wheel_robot wheel_robot_control.launch
```
次に２つ目の端末で以下のコマンドを入力するとコントローラーが立ち上がる
```
$ rqt
```
スライドバーを操作してロボットを動かせる


### 2.2 移動ロボットのナビゲーション   
以下のコマンドを別々の端末で入力する
```
$ roslaunch wheel_robot wheel_robot_control.launch

$ rosrun odom_listener odom_listener

$ rqt
```
順に実行することでGazeboが立ち上がる。

rqtでロボットを操作し２番めの端末を終了するとホームディレクトリにwaypoints.yamlファイルが保存される  
 
  <br>
次にwaypoints.yamlファイルを" ~/catkin_ws/src/wheel_robot_nav/waypoints"のディレクトリに置く 
そして、以下のコマンドを別々の端末で入力する


```bash
$ roslaunch wheel_robot wheel_robot_control.launch 

$ roslaunch wheel_robot_nav wheel_robot_nav.launch 

```


ロボットがwaypointに従って移動をするのが確認できる
  <br>

### 2.3 移動ロボットでの地図作成(slam gmapping)  

以下のコマンドを別々の端末で入力する
```bash
$ roslaunch wheel_robot wheel_robot.launch 

$ roslaunch wheel_robot_gmapping wheel_robot_gmapping.launch

$ rqt　

$ rosrun rviz rviz

```

Rvizの"Add"をクリックし、"RobotModel","Map"を追加すると地図生成の様子を確認できる 

次に地図を保存する
保存したいフォルダに移動し、以下のコマンドを入力する
```bash
$ rosrun map_server map_saver -f test

```

"test.pgm","test.yaml"が生成される
  <br>
  
### 2.4 差動2輪移動ロボットの作り方


以下のコマンドを入力する
```
$ roslaunch diff_mobile_robot diff_mobile_gazebo.launch

```bash

Gazeboシミュレータ上で作成したロボットが表示される
  <br>

### 5. 差動2輪移動ロボットのナビゲーションの作り方

以下のコマンドを別々の端末で入力する
```bash
$ roslaunch diff_mobile_robot diff_mobile_gazebo.launch

$ roslaunch diff_mobile_robot gmapping.launch

$ rqt

```

差動2輪移動ロボットを用いてマップを作成できる

地図を保存する場合は保存したいフォルダに移動し、以下のコマンドを入力する
```bash
$ rosrun map_server map_saver -f testmap

```

  <br>
 
以下のコマンドを入力する
```bash
$ roslaunch diff_mobile_robot diff_mobile_gazebo.launch

$ roslaunch diff_mobile_robot amcl.launch

$ rviz

$ roslaunch diff_mobile_robot diff_mobile_robot_nav.launch

```


ロボットが目的地まで移動する
