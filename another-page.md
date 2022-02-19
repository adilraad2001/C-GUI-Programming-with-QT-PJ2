
![imageqtc++](https://user-images.githubusercontent.com/93833171/142740904-ae7f6458-f497-47b1-b81f-f530250d112c.png)
 * * *
# <span style="color:red">Introduction:</span>

   in this second part follows up to add interactive functionality to the calculator widgets written in the previous homework. The goal is to use **Signals** and **Slots** to simulate a basic calculator behavior. The supported operations are *, +, -, /...etc.

> **Signal** : this is a message sent by a widget when an event occurs. 
            
              example: we clicked on a button.
              
              
              
> **Slot** : this is the function that is called when an event has occurred. It is said that the signal calls the slot. Concretely, a slot is a method of a class. 
            
              example:The quit() slot of the QApplication class invokes the termination of the program.

and at the end we will use the **QTimer** to simulate a traffic light .     
      

 * * *
 * * *

# <span style="color:red"> Summary:</span>
 * [Calculator](#Calcul)

 * [Traffic Light](#Traffic)

* * *
# Now we'll start wby:**Calculator**  {#Calcul}
The Calculator class provides a simple calculator widget. It inherits from QDialog and has several private slots associated with the calculator's buttons.
<span style="color:orange">main.cpp</span>
```cpp
#include "calculator.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Calculator w;
    w.setWindowTitle("Calculator");
    w.resize(500,500);
    w.show();
    return a.exec();
}
```




### <span style="color:orange">Calculator.cpp</span>

```cpp

#include "calculator.h"
#include <QKeyEvent>
#include <QApplication>
#include <math.h>


Calculator::Calculator(QWidget *parent)
    : QWidget(parent)
{
    createWidgets();
    placeWidget();
    makeConnexions();
     l=0;

}

Calculator::~Calculator()
{
    delete disp;
    delete layout;
    delete buttonsLayout;
}


void Calculator::createWidgets()
{
    //Creating the layouts
    layout = new QVBoxLayout();
    layout->setSpacing(2);

    //grid layout
    buttonsLayout = new QGridLayout;
    setStyleSheet("QPushButton{   background-color: Green;  color: white; padding: 15px 32px;  font-size: 20px;  }");
    //creating the buttons of digits number
    for(int i=0; i < 10; i++)
    {
        digits.push_back(new QPushButton(QString::number(i)));
        digits.back()->setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Fixed);
        digits.back()->resize(sizeHint().width(), sizeHint().height());
        digits.back()->setStyleSheet("QPushButton:hover{ background-color: #f44336; color: white;}");
    }


    //enter button
    enter = new QPushButton("Enter",this);
    enter->setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Fixed);
    enter->resize(sizeHint().width(), sizeHint().height());
//clear button for clear  everry input
    Clear = new QPushButton("Clear",this);
    Clear->setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Fixed);
    Clear->resize(sizeHint().width(), sizeHint().height());

//Button for affiche the porcentage of numbers
    reste = new QPushButton("%",this);
    reste->setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Fixed);
    reste->resize(sizeHint().width(), sizeHint().height());

    //operatiosn buttons with style

    operations.push_back(new QPushButton("+"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3;; color: white;}");
    operations.push_back(new QPushButton("-"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3; color: white;}");
    operations.push_back(new QPushButton("*"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3;; color: white;}");
    operations.push_back(new QPushButton("/"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3;; color: white;}");
    operations.push_back(new QPushButton("%"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3;; color: white;}");
    operations.push_back(new QPushButton("+/-"));
    operations.back()->setStyleSheet("QPushButton:hover{ background-color: #097DE3;; color: white;}");
//trigonomy operation

    trigono.push_back(new QPushButton("cos"));
    trigono.back()->setStyleSheet("QPushButton:hover{ background-color: #F0E569; color: white;}");
    trigono.push_back(new QPushButton("sin"));
    trigono.back()->setStyleSheet("QPushButton:hover{ background-color: #02FA3D; color: white;}");
    trigono.push_back(new QPushButton("tan"));
    trigono.back()->setStyleSheet("QPushButton:hover{ background-color: #F02E00; color: white;}");
    trigono.push_back(new QPushButton("acos"));
    trigono.back()->setStyleSheet("QPushButton:hover{ background-color: #A902FA; color: white;}");
    trigono.push_back(new QPushButton("asin"));
    trigono.back()->setStyleSheet("QPushButton:hover{ background-color: #F03DD1; color: white;}");
    Clear->setStyleSheet("QPushButton:hover{ background-color: #f44336; color: white;}");
    enter->setStyleSheet("QPushButton:hover{ background-color: #f44336; color: white;}");
    //creating the lcd
    disp = new QLCDNumber(this);
    disp->setDigitCount(6); //for the max number of mdigits is 6 
        disp->setStyleSheet("QLCDNumber{ background-color: rgb(0, 0, 0);border: 2px solid rgb(113, 113, 113);border-width: 2px;border-radius: 10px; color: rgb(255, 255, 255);}");

}
void Calculator::placeWidget()
{ /for place every push button in the layout
    layout->addWidget(disp);
    layout->addLayout(buttonsLayout);
    //adding the buttons
    for(int i=1; i <10; i++)
        buttonsLayout->addWidget(digits[i], (i-1)/3, (i-1)%3);
    //Adding the operations
    for(int i=0; i < 4; i++){
        buttonsLayout->addWidget(operations[ i], i, 4);
    }
    for(int i=0; i < 5; i++){
        buttonsLayout->addWidget(trigono[ i], i, 5);
       }

    //Adding the 0 button
    buttonsLayout->addWidget(digits[0], 3, 0);
    buttonsLayout->addWidget(operations[4],3,1);
    buttonsLayout->addWidget(operations[5],3,2);
    buttonsLayout->addWidget(enter, 4, 0,1,2);
    buttonsLayout->addWidget(Clear,4,2,1,3);
    setLayout(layout);
}

void Calculator::makeConnexions()
{
    //Connecting the digits
    for(int i=0; i <10; i++)
        connect(digits[i], &QPushButton::clicked,
                this, &Calculator::newDigit);
                //connectin of the operation
    for(int i=0; i <6; i++)
            connect(operations[i], &QPushButton::clicked,
                    this, &Calculator::changeOperation);
                    //connecting of  equal button
    connect(enter,&QPushButton::clicked,this,&Calculator::result);
                        //connecting of  clear button

        connect(Clear,&QPushButton::clicked,this,&Calculator::clea);
                    //connecting of  trigono operation

        for(int i=0; i <5; i++)

                connect(trigono[i], &QPushButton::clicked,
                        this, &Calculator::tri);


}

void Calculator::keyPressEvent(QKeyEvent *e)
{
//for presse the button fom the keypad or keyboard
    //Exiting the application by a click on space
    if( e->key() == Qt::Key_Escape)
        qApp->exit(0);
    if( e->key() == Qt::Key_0)
        newdig(e);
    if( e->key() == Qt::Key_1)
        newdig(e);
    if( e->key() == Qt::Key_2)
        newdig(e);
    if( e->key() == Qt::Key_3)
        newdig(e);
    if( e->key() == Qt::Key_4)
        newdig(e);
    if( e->key() == Qt::Key_5)
        newdig(e);
    if( e->key() == Qt::Key_6)
        newdig(e);
    if( e->key() == Qt::Key_7)
        newdig(e);
    if( e->key() == Qt::Key_8)
        newdig(e);
    if( e->key() == Qt::Key_9)
        newdig(e);
    if( e->key() == Qt::Key_Plus)
        newoperator(e);
    if( e->key() == Qt::Key_Minus)
        newoperator(e);
    if( e->key() == Qt::Key_multiply)
        newoperator(e);
    if( e->key() == Qt::Key_division)
        newoperator(e);
    if( e->key() == Qt::Key_Enter)
        result();
}
void Calculator::newDigit( )
{
//fonction for write the number who have more than 1 number
    //getting the sender
    auto button = dynamic_cast<QPushButton*>(sender());
    //getting the value
    float value = button->text().toInt();
    //Check if we have an operation defined
    if(operation)
    {
        //check if we have a value or not
        if(!right)
            right = new float{value};
        else
            *right = 10 * (*right) + value;

        disp->display(*right);

    }
    else
    {
        if(!left)
            left = new float{value};
        else
            *left = 10 * (*left) + value;

        disp->display(*left);
    }
//condition for stack the number in variable his name z whine the utilisateur enter new digit for operation of more than two numbers
    if(ab==0){
   z[ab]=*left ;
    }else{z[ab]=*right;}
}
void Calculator::changeOperation()
{
    ab++;


    if(e==0){
        auto button = dynamic_cast<QPushButton*>(sender());//for get the operation from the pushbutton pressed and stock in button

        //Storing the operation
     operation = new QString({button->text()}) ;
        //Initiating the right button
    e=button->text();
    hg[k]=e;//variable for stock the operatio  in table named hg[] for operation of more than two numbers
k++;
     right = new float{0};

     //reseting the display
     disp->display(0);
}else{
    //Getting the sender button
    auto button = dynamic_cast<QPushButton*>(sender());
 e=button->text();
 hg[k]=e;
k++;
//this condition for do the operation and stock the result in the left variable for operation of more than two numbers  because the variable left the only  remmember the number
 if(e==QString("+")){


     *left=(*left+(*right));

 }
 if(e=="-"){
     *left=(*left-(*right));
 }
 if(e=="*"){
     *left=(*left*(*right));
 }
    right = new float{0};
    //reseting the display

    disp->display(0);
    l++;}

}
void Calculator::result(){
//here for show the result of any operation after pressed the enter button

    if(*operation=="+"){
        disp->display(*left+(*right));
    }
    if(*operation=="-"){
        disp->display(*left-(*right));
    }
    if(*operation=="*"){
        disp->display(*left*(*right));
    }
    if(*operation=="/"){

            disp->display(*left/(*right));

    }
    if(*operation=="%"){
         disp->display(*left*(0.01));
    }
    if(*operation=="+/-"){
         disp->display(*left*(-1));
    }
    *left= *left+(*right);
    *right=0;

    *operation="";

}
void Calculator::clea(){
//reset button
 right=nullptr;
    left=nullptr;
    operation=nullptr;
    disp->display(0);
}

void Calculator::tri(){
    //Getting the sender button
    //fonction for the trigono operation
    auto button = dynamic_cast<QPushButton*>(sender());
    //Storing the operation
    operation = new QString{button->text()};

    if(*operation=="cos"){
        disp->display(cos(*left));
    }
    if(*operation=="sin"){
        disp->display(sin(*left));
    }
    if(*operation=="tan"){
        disp->display(tan(*left));
    }
    if(*operation=="acos"){
        disp->display(acos(*left));
    }
    if(*operation=="asin"){
        disp->display(asin(*left));
    }

          }
void Calculator::newdig(QKeyEvent *e)
{
    //getting the sender
//fonction for accept the number from the keypad

    //getting the value
    float value = e->text().toInt();

    //Check if we have an operation defined
    if(operation)
    {
        //check if we have a value or not
        if(!right)
            right = new float{value};
        else
            *right = 10 * (*right) + value;

        disp->display(*right);

    }
    else
    {
        if(!left)
            left = new float{value};
        else
            *left = 10 * (*left) + value;

        disp->display(*left);
    }
        }
void Calculator::newoperator(QKeyEvent *e){
    operation = new QString{e->text()};

    //Initiating the right button
    right = new float{0};

    //reseting the display
    disp->display(0);
                                          }
```

### <span style="color:orange">Calculator.h</span>


```cpp

   class Calculator : public QWidget
{
    Q_OBJECT
public:
    Calculator(QWidget *parent = nullptr);
    ~Calculator();
//using slot for fix the  classical problem which to map multiple signals to the same slot. The slot has to behave differently according the which digit was pressed.
public slots:
   void newDigit();
   void changeOperation();
   void result();
   void clea();
   void tri();




 // Add you custom slots here

protected:
    void createWidgets();        //Function to create the widgets
    void placeWidget();         // Function to place the widgets
    void makeConnexions();// Create all the connectivity

//events
protected:
    void keyPressEvent(QKeyEvent *e)override;     //Override the keypress events
    void newdig(QKeyEvent *e);
    void newoperator(QKeyEvent *e);

private:

    QGridLayout *buttonsLayout; // layout for the buttons
    QVBoxLayout *layout;        //main layout for the button
    QVector<QPushButton*> digits;  //Vector for the digits
    QPushButton *enter;            // enter button
    QPushButton *Clear;
    QPushButton *reste;
    int z[9];
    int l;
    int k=0;
    QString hg[9];
    int ab=0;
    QString e=0;

    QVector<QString*> cb;
QString *trig=nullptr;
    QVector<QPushButton*> operations; //operation buttons
    QVector<QPushButton*> trigono;
    QLCDNumber *disp;
    float * right=nullptr;     // right operand
    // Where to display the numbers
    float * left=nullptr; //left operand

    QString *operation=nullptr;
    // Pointer on the current operation


};

```

# Result
![2021-11-21 19_44_18-main cpp @ calculator - Qt Creator](https://user-images.githubusercontent.com/93819249/142775775-8179056d-be6f-4528-9d24-7bcc812acf69.png)


* *  *
# here **Traffic Light**  {#Traffic}

<span style="color:orange">main.cpp</span>

```cpp
#include <QApplication>
#include "trafficlight.h"

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);


    //Creating the traffic light
    auto light = new TrafficLight;


    //showing the trafic light
    light->show();

    return a.exec();
}

```

<span style="color:orange">trafficlight.cpp</span>

```cpp

#include "trafficlight.h"
#include <QWidget>
#include <QLayout>
#include <QRadioButton>
#include <QTime>
#include <QApplication>


TrafficLight::TrafficLight(QWidget * parent): QWidget(parent){
    //Creatign the widgets
    createWidgets();
    //place Widgets
    placeWidgets();
}

//our createwidgets() function
void TrafficLight::createWidgets()
{
  QTime currentTime = QTime::currentTime();

  redlight = new QRadioButton;

  //this for the red color
  redlight->setEnabled(false);
  redlight->toggle();
  //redlight->isChecked();
  redlight->setStyleSheet("QRadioButton::indicator:checked { background-color: red;}");

  //this for the yellow color
  yellowlight = new QRadioButton;
  yellowlight->setEnabled(false);
  //yellowlight->toggle();
  yellowlight->setStyleSheet("QRadioButton::indicator:checked { background-color: yellow;}");

  //this for the green color
  greenlight = new QRadioButton;
  greenlight->setEnabled(false);
  //greenlight->toggle();
  greenlight->setStyleSheet("QRadioButton::indicator:checked { background-color: green;}");

  //here we add each element of the colors at the end ofQradiobutton
  lights.append(redlight);
  lights.append(yellowlight);
  lights.append(greenlight);

  index = 0;
  //here you want to change the value that exists between startTimer for example : startTimer(500)
  //starting the timer with an interval in milliseconds
  startTimer(200);
}

//our placeWidgets() function
void TrafficLight::placeWidgets()
{

  // Placing the widgets

  auto layout = new QVBoxLayout;//our QVboxlayout
  layout->addWidget(redlight);//we added redlight to layout
  layout->addWidget(yellowlight);//we added yellowight to layout
  layout->addWidget(greenlight);//we added greenlightto layout
  setLayout(layout);
}

void TrafficLight::timerEvent(QTimerEvent *e)
{

index = (index + 1)%3;
lights[index]->toggle();
    currentTime++;
    //if the yellow-light is on(checked) and pass his 4 second  active the redlight and initiate the currenttime 0

    if(redlight->isChecked()&& currentTime==4){
        yellowlight->toggle();
    currentTime=0;
    }
    //same thing for the yellow color
    if(yellowlight->isChecked()&& currentTime==3){
        greenlight->toggle();
    currentTime=0;
    }
    //same thing for the green color
    if(greenlight->isChecked()&& currentTime==1){
        redlight->toggle();
    currentTime=0;
    }
}

//our keyPressEvent() function
void TrafficLight::keyPressEvent(QKeyEvent *e){

//    if(e->key()==Qt::Key_Escape)
//        qApp->exit;
//    if(e->key()==Qt::Key_R)
//        index=0;
//    lights[index]->toggle();
//    if(e->key()==Qt::Key_G)
//        index=1;
//    lights[index]->toggle();
//    if(e->key()==Qt::Key_Y)
//        index=2;
//    lights[index]->toggle();

}
```
<span style="color:orange">trafficlight.h</span>
```h
#ifndef TRAFFIC_LIGHT_H
#define TRAFFIC_LIGHT_H

#include <QWidget>
#include <QLabel>
#include <QTime>
#include <QVector>
#include<QKeyEvent>

class QRadioButton;

class TrafficLight: public QWidget{
  Q_OBJECT

public:

  TrafficLight(QWidget * parent = nullptr);
  void timerEvent(QTimerEvent *e);
  void keyPressEvent(QKeyEvent *e);

protected:
     void createWidgets();
     void placeWidgets();

private:
     //our Qradiobutton
  QRadioButton * redlight;
  QRadioButton * yellowlight;
  QRadioButton * greenlight;
  //our qlable
  QLabel * timeLabel;
  //our vector of QRadioButton
  QVector <QRadioButton*> lights;
  int index ;
  int times[3]{4,1,2};
  int currentTime;

};


#endif
```

***
## Conclusion
After the completion of the work, some of the desired results were reached, but some were not reached due to the lack of clarity in some parts and the difficulty of applying ideas with the available tools such as an LCD.like two operations A process can be performed for more than three operations because it works only if the operations are between + and - as for / and * do not work and give a result without calculating the priority

