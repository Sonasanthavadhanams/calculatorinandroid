# Ex.No:5 Develop a program to create a simple calculator using android studio.

## Aim:
To create and design an android application for a simple calculator using android studio.

## EQUIPMENTS REQUIRED:

Android Studio(Latest Version)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as smsintent and click Next.

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6:Display details give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:

### Program to create and design an android application simple calculator using Intent.

Developed by : SONA S

Registeration Number : 212224110049

### MainActivity.java

```
package com.example.calculatorapplication;

import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.text.DecimalFormat;
import java.text.DecimalFormatSymbols;
import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private TextView txtResult;

    private String firstNumber = "";
    private String secondNumber = "";
    private String operator = "";

    private boolean isSecondNumber = false;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        txtResult = findViewById(R.id.txtResult);

        // Number Buttons
        int[] numberButtons = {
                R.id.btn0, R.id.btn00, R.id.btn1, R.id.btn2, R.id.btn3,
                R.id.btn4, R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9
        };

        for (int id : numberButtons) {
            Button btn = findViewById(id);
            if (btn != null) {
                btn.setOnClickListener(v -> {
                    animateButton(btn);
                    appendNumber(btn.getText().toString());
                });
            }
        }

        // Decimal Button
        Button btnDot = findViewById(R.id.btnDot);
        if (btnDot != null) {
            btnDot.setOnClickListener(v -> {
                animateButton(btnDot);
                appendDot();
            });
        }

        // Operator Buttons
        setupOperatorButton(R.id.btnPlus, "+");
        setupOperatorButton(R.id.btnMinus, "-");
        setupOperatorButton(R.id.btnMultiply, "×");
        setupOperatorButton(R.id.btnDivide, "÷");

        // Percentage
        Button btnPercent = findViewById(R.id.btnPercent);
        if (btnPercent != null) {
            btnPercent.setOnClickListener(v -> {
                animateButton(btnPercent);
                percentage();
            });
        }

        // Clear
        Button btnC = findViewById(R.id.btnC);
        if (btnC != null) {
            btnC.setOnClickListener(v -> {
                animateButton(btnC);
                clear();
            });
        }

        // Delete
        Button btnDel = findViewById(R.id.btnDel);
        if (btnDel != null) {
            btnDel.setOnClickListener(v -> {
                animateButton(btnDel);
                deleteLast();
            });
        }

        // Equal
        Button btnEqual = findViewById(R.id.btnEqual);
        if (btnEqual != null) {
            btnEqual.setOnClickListener(v -> {
                animateButton(btnEqual);
                calculate();
            });
        }
    }

    private void setupOperatorButton(int id, String op) {
        Button btn = findViewById(id);
        if (btn != null) {
            btn.setOnClickListener(v -> {
                animateButton(btn);
                setOperator(op);
            });
        }
    }

    // Button Press Animation
    private void animateButton(Button button) {
        button.animate()
                .scaleX(0.90f)
                .scaleY(0.90f)
                .setDuration(80)
                .withEndAction(() ->
                        button.animate()
                                .scaleX(1f)
                                .scaleY(1f)
                                .setDuration(80)
                                .start())
                .start();
    }

    // Append Numbers
    private void appendNumber(String number) {
        if (!isSecondNumber) {
            if (firstNumber.isEmpty()) {
                if (number.equals("00")) {
                    firstNumber = "0";
                } else {
                    firstNumber = number;
                }
            } else if (firstNumber.equals("0")) {
                if (number.equals("0") || number.equals("00")) return;
                firstNumber = number;
            } else {
                firstNumber += number;
            }
        } else {
            if (secondNumber.isEmpty()) {
                if (number.equals("00")) {
                    secondNumber = "0";
                } else {
                    secondNumber = number;
                }
            } else if (secondNumber.equals("0")) {
                if (number.equals("0") || number.equals("00")) return;
                secondNumber = number;
            } else {
                secondNumber += number;
            }
        }
        updateDisplay();
    }

    // Decimal Point
    private void appendDot() {
        if (!isSecondNumber) {
            if (!firstNumber.contains(".")) {
                if (firstNumber.isEmpty() || firstNumber.equals("-")) {
                    firstNumber += "0";
                }
                firstNumber += ".";
            }
        } else {
            if (!secondNumber.contains(".")) {
                if (secondNumber.isEmpty() || secondNumber.equals("-")) {
                    secondNumber += "0";
                }
                secondNumber += ".";
            }
        }
        updateDisplay();
    }

    // Set Operator
    private void setOperator(String op) {
        if (firstNumber.isEmpty()) {
            if (op.equals("-")) {
                firstNumber = "-";
                updateDisplay();
            }
            return;
        }

        if (firstNumber.equals("-")) return;

        if (isSecondNumber && secondNumber.isEmpty()) {
            if (op.equals("-")) {
                secondNumber = "-";
                updateDisplay();
                return;
            }
            // Switch operator if another operator button is pressed
            operator = op;
            updateDisplay();
            return;
        }

        // If we already have a second number, calculate first (chaining)
        if (!secondNumber.isEmpty() && !secondNumber.equals("-")) {
            calculate();
        }

        operator = op;
        isSecondNumber = true;
        updateDisplay();
    }

    // Calculate Result
    private void calculate() {
        if (firstNumber.isEmpty() || secondNumber.isEmpty() || operator.isEmpty()
                || firstNumber.equals("-") || secondNumber.equals("-")) {
            return;
        }

        try {
            double num1 = Double.parseDouble(firstNumber);
            double num2 = Double.parseDouble(secondNumber);
            double result;

            switch (operator) {
                case "+": result = num1 + num2; break;
                case "-": result = num1 - num2; break;
                case "×": result = num1 * num2; break;
                case "÷":
                    if (num2 == 0) {
                        Toast.makeText(this, "Cannot divide by zero", Toast.LENGTH_SHORT).show();
                        clear();
                        return;
                    }
                    result = num1 / num2;
                    break;
                default: return;
            }

            String answer = formatResult(result);
            txtResult.setText(answer);

            // Prepare for next calculation
            firstNumber = answer.equals("Error") ? "" : answer;
            secondNumber = "";
            operator = "";
            isSecondNumber = false;
        } catch (NumberFormatException e) {
            Toast.makeText(this, "Error in calculation", Toast.LENGTH_SHORT).show();
            clear();
        }
    }

    private String formatResult(double result) {
        if (Double.isNaN(result) || Double.isInfinite(result)) {
            return "Error";
        }
        if (result == (long) result) {
            return String.valueOf((long) result);
        } else {
            DecimalFormat df = new DecimalFormat("#.##########", DecimalFormatSymbols.getInstance(Locale.US));
            return df.format(result);
        }
    }

    // Percentage
    private void percentage() {
        String target = isSecondNumber && !secondNumber.isEmpty() ? secondNumber : firstNumber;
        if (target.isEmpty() || target.equals("-")) return;

        try {
            double value = Double.parseDouble(target) / 100.0;
            String answer = formatResult(value);

            if (isSecondNumber && !secondNumber.isEmpty()) {
                secondNumber = answer;
            } else {
                firstNumber = answer;
            }
            updateDisplay();
        } catch (NumberFormatException e) {
            Toast.makeText(this, "Invalid number", Toast.LENGTH_SHORT).show();
        }
    }

    // Delete Last Character
    private void deleteLast() {
        if (!isSecondNumber) {
            if (!firstNumber.isEmpty()) {
                firstNumber = firstNumber.substring(0, firstNumber.length() - 1);
            }
        } else {
            if (!secondNumber.isEmpty()) {
                secondNumber = secondNumber.substring(0, secondNumber.length() - 1);
            } else {
                operator = "";
                isSecondNumber = false;
            }
        }
        updateDisplay();
    }

    // Clear Calculator
    private void clear() {
        firstNumber = "";
        secondNumber = "";
        operator = "";
        isSecondNumber = false;
        updateDisplay();
    }

    private void updateDisplay() {
        if (!isSecondNumber) {
            txtResult.setText(firstNumber.isEmpty() ? "0" : firstNumber);
        } else {
            String display = firstNumber + " " + operator + (secondNumber.isEmpty() ? "" : " " + secondNumber);
            txtResult.setText(display);
        }
    }
}

```

### activity_main.xml

```

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:background="@color/background"
    android:padding="16dp">

    <!-- Display -->
    <TextView
        android:id="@+id/txtResult"
        android:layout_width="match_parent"
        android:layout_height="120dp"
        android:gravity="end|center_vertical"
        android:padding="20dp"
        android:text="0"
        android:textColor="@color/white"
        android:textSize="48sp"
        android:textStyle="bold" />

    <!-- Buttons -->
    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="4"
        android:alignmentMode="alignMargins"
        android:useDefaultMargins="true">

        <!-- Row 1 -->

        <Button
            android:id="@+id/btnC"
            style="@style/CalcButtonRed"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="C" />

        <Button
            android:id="@+id/btnDel"
            style="@style/CalcButtonBlue"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="⌫" />

        <Button
            android:id="@+id/btnPercent"
            style="@style/CalcButtonOrange"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="%" />

        <Button
            android:id="@+id/btnDivide"
            style="@style/CalcButtonOrange"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="÷" />

        <!-- Row 2 -->

        <Button
            android:id="@+id/btn7"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="7" />

        <Button
            android:id="@+id/btn8"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="8" />

        <Button
            android:id="@+id/btn9"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="9" />

        <Button
            android:id="@+id/btnMultiply"
            style="@style/CalcButtonOrange"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="×" />

        <!-- Row 3 -->

        <Button
            android:id="@+id/btn4"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="4" />

        <Button
            android:id="@+id/btn5"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="5" />

        <Button
            android:id="@+id/btn6"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="6" />

        <Button
            android:id="@+id/btnMinus"
            style="@style/CalcButtonOrange"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="-" />

        <!-- Row 4 -->

        <Button
            android:id="@+id/btn1"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="1" />

        <Button
            android:id="@+id/btn2"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="2" />

        <Button
            android:id="@+id/btn3"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="3" />

        <Button
            android:id="@+id/btnPlus"
            style="@style/CalcButtonOrange"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="+" />

        <!-- Row 5 -->

        <Button
            android:id="@+id/btn00"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="00" />

        <Button
            android:id="@+id/btn0"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="0" />

        <Button
            android:id="@+id/btnDot"
            style="@style/CalcButtonWhite"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="." />

        <Button
            android:id="@+id/btnEqual"
            style="@style/CalcButtonGreen"
            android:elevation="6dp"
            android:stateListAnimator="@null"
            android:text="=" />

    </GridLayout>

</LinearLayout>

```


### AndroidManifest.xml

```

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Calculator">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>

</manifest>

```
### values\themes.xml

```

<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools">

    <style name="Theme.Calculator"
        parent="Theme.Material3.DayNight.NoActionBar">
        <!-- Primary Colors -->
        <item name="colorPrimary">@color/operatorButton</item>
        <item name="colorPrimaryContainer">@color/operatorButton</item>
        <!-- Background -->
        <item name="android:windowBackground">@color/background</item>
        <!-- Status Bar -->
        <item name="android:statusBarColor">@color/background</item>
        <item name="android:navigationBarColor">@color/background</item>
        <!-- Light status bar icons -->
        <item name="android:windowLightStatusBar">false</item>
    </style>

</resources>

```

### night\themes.xml

```
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools">

    <style name="Theme.Calculator"
        parent="Theme.Material3.DayNight.NoActionBar">
        <!-- Primary Colors -->
        <item name="colorPrimary">@color/operatorButton</item>
        <item name="colorPrimaryContainer">@color/operatorButton</item>
        <!-- Background -->
        <item name="android:windowBackground">@color/background</item>
        <!-- Status Bar -->
        <item name="android:statusBarColor">@color/background</item>
        <item name="android:navigationBarColor">@color/background</item>
        <!-- Dark mode -->
        <item name="android:windowLightStatusBar">false</item>
    </style>

</resources>

```

## OUTPUT:

### Starting Page

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0c7076a2-def5-41d4-ac55-46272d729f13" />

### Basic Calculation - Addition

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/362cd2e4-bf50-413b-9cde-2bffba2df4b7" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f885e730-45a5-499b-b6c7-9c3a5abad560" />

### Basic Calculation - Subtraction

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2acd0e25-4a4f-4306-9a9e-d3abd0045f9b" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/839f872b-0394-470b-ae4d-93cb09c44ace" />

### Basic Calculation - Multiplication

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/6f686bf1-3fbb-486e-978c-8fbbe98688dc" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/73da450d-2761-41a3-a7be-82b8c655f07f" />

### Basic Calculation - Division

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/38d68588-37c1-4845-abf3-64b8707f7429" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c0385c65-f12b-431e-b230-8238f84a5d51" />

## RESULT:

Thus, a Simple Android Application create a simple calculator using Android Studio is developed and executed successfully.



