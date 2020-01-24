# 2020NKO研製作物メモ

## 各ソースコードについて

- kiban.ino

  メインファイル。setup()、loop()、interrupt()内に記述すれば動きます

  - setup()

    起動時に一回だけ呼ばれる。初期化など一回動かせば済むものを置く

  - loop()

    setup()終了後、電源が切れるまで動き続ける。main関数

  - interrupt()

    タイマ3の割り込み関数。setup()内で別の関数にも指定できる

e.g.

~~~arduino
void setup() {
    // タイマ3の初期化
    Timer3.initialize(T3_DT); 
    Timer3.attachInterrupt(interrupt_TMR3);
}
~~~

- defines.h

  各ピンの設定やゲインなど、変更が考えられる定数値をまとめて定義してる

- pinSetting.h

  モーターやエンコーダに繋げるピンの設定と宣言

- types.h

  クラスと構造体の定義

- types.cpp

  types.h の中身。Jointクラス内の関数の実装

  

## モーターを動かすには

1. Jointクラスの宣言

   ~~~
   Joint foo = {DirectionPin, PWMPin, KP, KI, KD};
   ~~~

   DirectionPin:方向決定ピン。モーターの正転時にLOW、逆転時にHIGHを出す。

   PWMPin:PWM出力ピン。

   KP, KI,KD:PIDの各ゲイン

   

2. Jointクラス内のmoveMotor()を使って動かす

   1. 生の値値を入れて動かす

      ~~~
      (適当な関数内)
      foo.moveMotor(val);  // val:適当な変数・定数
      ~~~

      

   2. PIDして動かす

      ~~~
      Joint foo = {DirectionPin, PWMPin, KP, KI, KD};
      
      void loop(){
          foo.setObjAng(180);  // 目標角度を180パルス目に設定
      }
      
      void interrupt(){
          readEncoder();  // エンコーダーを読む関数。適当に実装。
          foo.moveMotor(foo.anglePID());  // 目標角度までPIDで動かす
      }
      ~~~

   

## エンコーダーの値を読むには

使用しているライブラリ: https://github.com/PaulStoffregen/Encoder

1.  エンコーダーの宣言

   ~~~
   Encoder bar = {pinA, pinB};
   ~~~

   pinA:エンコーダのA層につながるピン

   pinB:エンコーダのB層につながるピン

   Z層など三層以上のエンコーダはAB以外使わないので、どこにも接続しない

2. 読む

   ~~~
   pulses = bar.read();
   ~~~

   Encoderライブラリ内で勝手に読んでくれているので、エンコーダの値が必要になったタイミングで適宜read()関数を呼べばよい。

   4逓倍になっているので注意が必要

   
## モータドライバ
https://docs.google.com/document/d/1rgQzn-nWn-qcWNnHjDZvIYqUrdCeBQQxXA-TU3BF0AQ/edit これ
