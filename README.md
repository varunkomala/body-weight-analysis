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
    .positive {
      color: red; /* Red for Excess (+) */
    }
    .normal {
      color: green; /* Green for Normal (0) */
    }
    .negative {
      color: yellow; /* Yellow for Low (-) */
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
      if (bmi < 18.5) bmiCategory = 'Underweight';
      else if (bmi >= 18.5 && bmi < 24.9) bmiCategory = 'Normal';
      else if (bmi >= 25 && bmi < 29.9) bmiCategory = 'Overweight';
      else bmiCategory = 'Obese';

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

      // Ideal weight range (Devine formula)
      const idealWeightMin = (50 + 2.3 * ((height * 100 / 2.54) - 60)).toFixed(1);
      const idealWeightMax = (idealWeightMin * 1.1).toFixed(1);

      // Calculate body age (simplified estimate based on BMI and age)
      const bodyAge = age + (bmi - 22) * 0.5;

      // Define categories for metrics (Excess, Low, Normal)
      const getLevel = (value, type) => {
        if (type === 'bodyFat') {
          if (value < 10) return { level: 'normal', color: 'normal' }; // Green
          else if (value >= 10 && value <= 20) return { level: 'normal', color: 'normal' }; // Green
          else if (value > 20) return { level: 'excess', color: 'positive' }; // Red
        }
        // Add more conditions for visceral fat, skeletal muscle, etc.
        return { level: 'normal', color: 'normal' }; // Default to green (normal)
      };

      // Display results
      const resultsHTML = `
        <h2>Results for ${name}</h2>
        <p><strong>BMI:</strong> ${bmi.toFixed(2)} (${bmiCategory})</p>
        <p><strong>BMR:</strong> ${bmr.toFixed(2)} kcal/day</p>
        <p><strong>Body Fat Percentage:</strong> ${bodyFatPercentage.toFixed(2)}% <span class="${getLevel(bodyFatPercentage, 'bodyFat').color}">${getLevel(bodyFatPercentage, 'bodyFat').level}</span></p>
        <p><strong>Visceral Fat Estimate:</strong> ${visceralFat}%</p>
        <p><strong>Subcutaneous Fat Estimate:</strong> ${subcutaneousFat}%</p>
        <p><strong>Skeletal Muscle Estimate:</strong> ${skeletalMuscle} kg</p>
        <p><strong>Body Age:</strong> ${Math.round(bodyAge)}</p>
        <p><strong>Ideal Weight Range:</strong> ${idealWeightMin} kg to ${idealWeightMax} kg</p>
      `;
      document.getElementById('results').innerHTML = resultsHTML;
    });
  </script>
</body>
</html>
