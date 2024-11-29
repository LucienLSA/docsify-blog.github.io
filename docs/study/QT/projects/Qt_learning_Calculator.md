---
title: Qt学习记录-四则运算器
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
- algorithm
---

# 简单的四则计算器实现
新建好一个Qt-Widget小窗口，在cpp文件的构造函数中设置小窗口的大小、窗口名称和字体
```cpp
    this->setMaximumSize(200,280);
    this->setMinimumSize(200,280);
    this->setWindowTitle("计算器");

    QFont f("仿宋",12); // 字体对象
    ui->mainLineEdit->setFont(f);
```
在ui文件中加入计算器的pushButton，加入lineEdit为输入框，修改pushButton对象的名称。
点击按钮后，需要在lineEdit中记录显示字符串，在头文件的private中加入

```cpp
    QString expression;
```
右键pushButton，转到槽，信号clicked

```cpp
void Widget::on_oneButton_clicked()
{
    expression += "1"; // 在lineEdit中追加字符串
    ui->mainLineEdit->setText(expression); // 设置文本显示
}
```
除清空和删除之外，同样方法对其他的pushButton进行转到槽，在头文件中会自动生成槽函数声明，源文件中生成槽函数实现

清空expression和显示ui
```cpp
void Widget::on_clearButton_clicked()
{
    expression.clear();
    ui->mainLineEdit->clear();
}
```
删除键为←，可选择在pushButton中插入图片。图片放入项目根目录下，构造函数中加入
```cpp
    QIcon icon("路径"); // 对路径加上转义/
    ui->delButton->set(icon);
```
删除结尾的字符
```cpp
void Widget::on_delButton_clicked()
{
    expression.chop(1);
    ui->mainLineEdit->setText(expression); // 显示剩余字符
}
```
修改pushButton背景颜色，构造函数中加入
```cpp
ui->equalButton->setStyleSheet("background:gray");
```
等于符号的算法，可创建一个Calculator类，表示计算算法
```cpp
#ifndef CALCULATOR_H
#define CALCULATOR_H

#include <QString>

class Calculator {
public:
    static QString calculate(const QString& expression);
};

#endif // CALCULATOR_H

```
```cpp
#include "calculator.h"
#include <QStack>
#include <QDebug>

// Helper function to check if a character is an operator (+, -, *, /)
bool isOperator(QChar ch) {
    return ch == '+' || ch == '-' || ch == '*' || ch == '/';
}

// Helper function to perform arithmetic operations
double performOperation(double num1, double num2, QChar op) {
    switch (op.toLatin1()) {
        case '+':
            return num1 + num2;
        case '-':
            return num1 - num2;
        case '*':
            return num1 * num2;
        case '/':
            return num1 / num2;
        default:
            return 0.0;
    }
}

QString Calculator::calculate(const QString& expression) {
    QStack<double> numStack;
    QStack<QChar> opStack;

    // Loop through the expression characters
    for (int i = 0; i < expression.length(); ++i) {
        QChar ch = expression[i];
        // Skip whitespaces
        if (ch.isSpace()) {
            continue;
        }

        if (ch.isDigit() || ch == '.') {
            // If the character is a digit or a decimal point, extract the full number
            int j = i;
            while (j < expression.length() && (expression[j].isDigit() || expression[j] == '.')) {
                ++j;
            }
            QString numberStr = expression.mid(i, j - i);
            bool conversionSuccess = false;
            double number = numberStr.toDouble(&conversionSuccess);
            if (conversionSuccess) {
                numStack.push(number);
            } else {
                // Handle invalid number format
                return "Error: Invalid number format";
            }
            i = j - 1;
        } else if (isOperator(ch)) {
            // If the character is an operator, perform necessary calculations
            while (!opStack.isEmpty() && isOperator(opStack.top())) {
                QChar prevOp = opStack.top();
                opStack.pop();
                if (numStack.size() < 2) {
                    // Handle insufficient operands for an operator
                    return "Error: Insufficient operands for operator";
                }
                double num2 = numStack.top();
                numStack.pop();
                double num1 = numStack.top();
                numStack.pop();
                double result = performOperation(num1, num2, prevOp);
                numStack.push(result);
            }
            opStack.push(ch);
        } else if (ch == '(') {
            // If the character is an opening parenthesis, push it to the operator stack
            opStack.push(ch);
        } else if (ch == ')') {
            // If the character is a closing parenthesis, perform calculations until the matching opening parenthesis
            while (!opStack.isEmpty() && opStack.top() != '(') {
                QChar prevOp = opStack.top();
                opStack.pop();
                if (numStack.size() < 2) {
                    // Handle insufficient operands for an operator
                    return "Error: Insufficient operands for operator";
                }
                double num2 = numStack.top();
                numStack.pop();
                double num1 = numStack.top();
                numStack.pop();
                double result = performOperation(num1, num2, prevOp);
                numStack.push(result);
            }
            if (opStack.isEmpty()) {
                // Handle mismatched parentheses
                return "Error: Mismatched parentheses";
            }
            opStack.pop(); // Pop the matching opening parenthesis
        } else {
            // Handle invalid characters in the expression
            return "Error: Invalid character in expression";
        }
    }

    // Perform remaining calculations
    while (!opStack.isEmpty()) {
        QChar op = opStack.top();
        opStack.pop();
        if (numStack.size() < 2) {
            // Handle insufficient operands for an operator
            return "Error: Insufficient operands for operator";
        }
        double num2 = numStack.top();
        numStack.pop();
        double num1 = numStack.top();
        numStack.pop();
        double result = performOperation(num1, num2, op);
        numStack.push(result);
    }

    // The final result will be at the top of the numStack
    if (numStack.isEmpty()) {
        // Handle empty expression
        return "Error: Empty expression";
    } else {
        double finalResult = numStack.top();
        return QString::number(finalResult);
    }
}
```
在widget.cpp中引入头文件#include "calculator.h"