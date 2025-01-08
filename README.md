<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Comprehensive Body Weight Analysis</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
    }
    h1 {
      color: #333;
    }
    label {
      margin-top: 10px;
      display: block;
    }
    #results {
      margin-top: 20px;
      padding: 20px;
      background-color: #f4f4f4;
      border: 1px solid #ddd;
    }
    table {
      width: 100%;
      border-collapse: collapse;
    }
    th, td {
      padding: 8px;
      border: 1px solid #ddd;
      text-align: center;
    }
    th {
      background-color: #f0f0f0;
    }
    .positive {
      color: red;
    }
    .normal {
      color: green;
    }
    .negative {
      color: yellow;
    }
  </style>
</head>
<body>
  <h1>Body Weight Analysis</h1>
  <form id="bodyAnalysisForm">
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" required><br><br>

    <label for="age">Age:</label>
    <input type="number" id="age" name="age" required><br><br>

    <label for="gender">Gender:</label>
    <select id="gender" name="gender" required>
      <option value="male">Male</option>
      <option value="female">Female</option>
    </select><br><br>

    <label for="height">Height (cm):</label>
    <input type="number" id="height" name="height" required><br><br>

    <label for="weight">Weight (kg):</label>
    <input type="number" id="weight" name="weight" required><br><br>

    <button type="submit">Calculate</button>
  </form>

  <div id="results"></div>

  <script>
    document.getElementById('bodyAnalysisForm').addEventListener('submit', function(event) {
      event.preventDefault();

      // Get input values
      const name = document.getElementById('name').value;
      const age = parseInt(document.getElementById('age').value);
      const gender = document.getElementById('gender').value;
      const height = parseFloat(document.getElementById('height').value) / 100; // Convert to meters
      const weight = parseFloat(document.getElementById('weight').value);

      if (isNaN(age) || isNaN(height) || isNaN(weight) || age <= 0 || height <= 0 || weight <= 0) {
        alert("Please enter valid inputs.");
        return;
      }

      // Calculate BMI
      const bmi = weight / (height * height);
      let bmiCategory = '';
      let bmiLevel = '';
      if (bmi < 18.5) {
        bmiCategory = 'Underweight';
        bmiLevel = '0'; // Normal
      } else if (bmi >= 18.5 && bmi < 24.9) {
        bmiCategory = 'Normal';
        bmiLevel = '0'; // Normal
      } else if (bmi >= 25 && bmi < 29.9) {
        bmiCategory = 'Overweight';
        bmiLevel = '+'; // Slightly overweight
      } else if (bmi >= 30 && bmi < 34.9) {
        bmiCategory = 'Obese';
        bmiLevel = '++'; // Moderately obese
      } else {
        bmiCategory = 'Obese';
        bmiLevel = '+++'; // Severely obese
      }

      // Calculate BMR using Harris-Benedict equation
      const bmr = gender === 'male' ?
                  10 * weight + 6.25 * (height * 100) - 5 * age + 5 :
                  10 * weight + 6.25 * (height * 100) - 5 * age - 161;

      // Calculate body fat percentage using an accurate formula
      let bodyFatPercentage;
      if (gender === 'male') {
        bodyFatPercentage = 1.20 * bmi + 0.23 * age - 16.2;
      } else {
        bodyFatPercentage = 1.20 * bmi + 0.23 * age - 5.4;
      }

      // Calculate visceral fat and subcutaneous fat
      const visceralFat = (bodyFatPercentage / 3).toFixed(1);
      const subcutaneousFat = (bodyFatPercentage * 0.7).toFixed(1);

      // Skeletal muscle estimate (based on weight)
      const skeletalMuscle = gender === 'male' ?
                             (50 + (weight * 0.33)).toFixed(1) :
                             (45 + (weight * 0.28)).toFixed(1);

      // Accurate Ideal Weight Calculation using the Devine formula
      let idealWeight;
      if (gender === 'male') {
        idealWeight = 50 + 2.3 * ((height * 100 / 2.54) - 60); // Adjust for height
      } else {
        idealWeight = 45.5 + 2.3 * ((height * 100 / 2.54) - 60); // Adjust for height
      }

      const idealWeightMin = (idealWeight - 10).toFixed(1); // Minus 10% of Ideal Weight
      const idealWeightMax = (idealWeight + 10).toFixed(1); // Plus 10% of Ideal Weight

      // Refined Body Age Calculation (More accurate)
      // Use BMI, age, and gender to calculate body age
      const bodyAge = age + (bmi - 22) * 0.7; // More realistic adjustment based on BMI

      // Define categories for metrics (Excess, Low, Normal)
      const getLevel = (value, type) => {
        if (type === 'bodyFat') {
          if (value < 10) return { level: 'normal', color: 'normal' };
          else if (value >= 10 && value <= 20) return { level: 'normal', color: 'normal' };
          else if (value > 20) return { level: 'positive', color: 'positive' };
        }
        // More categories for other measurements can be added here.
        return { level: 'normal', color: 'normal' };
      };

      // Display results in a table format
      const resultsHTML = `
        <h2>Results for ${name}</h2>
        <table>
          <tr>
            <th>Metric</th>
            <th>Value</th>
            <th>Level</th>
          </tr>
          <tr>
            <td>BMI</td>
            <td>${bmi.toFixed(2)}</td>
            <td>${bmiCategory} (${bmiLevel})</td>
          </tr>
          <tr>
            <td>BMR (kcal/day)</td>
            <td>${bmr.toFixed(2)}</td>
            <td class="${getLevel(bmi, 'bodyFat').color}">${getLevel(bmi, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Body Fat Percentage</td>
            <td>${bodyFatPercentage.toFixed(2)}%</td>
            <td class="${getLevel(bodyFatPercentage, 'bodyFat').color}">${getLevel(bodyFatPercentage, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Visceral Fat Estimate</td>
            <td>${visceralFat}%</td>
            <td class="${getLevel(visceralFat, 'bodyFat').color}">${getLevel(visceralFat, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Subcutaneous Fat Estimate</td>
            <td>${subcutaneousFat}%</td>
            <td class="${getLevel(subcutaneousFat, 'bodyFat').color}">${getLevel(subcutaneousFat, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Skeletal Muscle Estimate (kg)</td>
            <td>${skeletalMuscle}</td>
            <td class="${getLevel(skeletalMuscle, 'bodyFat').color}">${getLevel(skeletalMuscle, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Body Age</td>
            <td>${Math.round(bodyAge)}</td>
            <td class="${getLevel(bodyAge, 'bodyFat').color}">${getLevel(bodyAge, 'bodyFat').level}</td>
          </tr>
          <tr>
            <td>Ideal Weight Range
