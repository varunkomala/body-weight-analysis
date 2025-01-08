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
    h1, h2 {
      color: #333;
    }
  </style>
</head>
<body>
  <h1>Comprehensive Body Weight Analysis</h1>
  <form id="bodyAnalysisForm">
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

      const age = parseInt(document.getElementById('age').value);
      const gender = document.getElementById('gender').value;
      const height = parseFloat(document.getElementById('height').value) / 100;  // in meters
      const weight = parseFloat(document.getElementById('weight').value);

      if (isNaN(age) || isNaN(height) || isNaN(weight) || age <= 0 || height <= 0 || weight <= 0) {
        alert("Please enter valid inputs.");
        return;
      }

      // BMI
      const bmi = weight / (height * height);
      let bmiCategory = '';
      if (bmi < 18.5) bmiCategory = 'Underweight';
      else if (bmi >= 18.5 && bmi < 24.9) bmiCategory = 'Normal';
      else if (bmi >= 25 && bmi < 29.9) bmiCategory = 'Overweight';
      else bmiCategory = 'Obese';

      // BMR
      const bmr = gender === 'male' ? 
                  88.36 + (13.4 * weight) + (4.8 * (height * 100)) - (5.7 * age) : 
                  447.6 + (9.2 * weight) + (3.1 * (height * 100)) - (4.3 * age);

      // Body Fat Percentage (Simplified Navy Method)
      const bodyFatPercentage = gender === 'male' ?
                                (1.39 * bmi) + (0.16 * age) - 10.34 : 
                                (1.39 * bmi) + (0.16 * age) - 9;

      // Visceral Fat Estimate (based on body fat %)
      const visceralFat = bodyFatPercentage > 15 ? ((bodyFatPercentage - 15) * 0.1 + 10).toFixed(1) : 9;

      // Subcutaneous Fat Estimate (using proportion)
      const subcutaneousFat = ((bodyFatPercentage * 0.75).toFixed(1));

      // Skeletal Muscle (Approximation)
      const skeletalMuscle = gender === 'male' ? 
                             (weight * 0.4).toFixed(1) : 
                             (weight * 0.35).toFixed(1);

      // Ideal Weight (using Devine formula for accuracy)
      const idealWeightMin = gender === 'male' ? 
                             (50 + 0.9 * ((height * 100) - 152)).toFixed(1) : 
                             (45.5 + 0.9 * ((height * 100) - 152)).toFixed(1);
      const idealWeightMax = (idealWeightMin * 1.1).toFixed(1);

      // Body Age Approximation
      const bodyAge = Math.round(age + (bmi - 22) * 0.5);

      const resultsHTML = `
        <h2>Results</h2>
        <p><strong>BMI:</strong> ${bmi.toFixed(2)} (${bmiCategory})</p>
        <p><strong>BMR:</strong> ${bmr.toFixed(2)} kcal/day</p>
        <p><strong>Body Fat Percentage:</strong> ${bodyFatPercentage.toFixed(2)}%</p>
        <p><strong>Visceral Fat Estimate:</strong> ${visceralFat}%</p>
        <p><strong>Subcutaneous Fat Estimate:</strong> ${subcutaneousFat}%</p>
        <p><strong>Skeletal Muscle Estimate:</strong> ${skeletalMuscle} kg</p>
        <p><strong>Body Age:</strong> ${bodyAge}</p>
        <p><strong>Ideal Weight Range:</strong> ${idealWeightMin} kg to ${idealWeightMax} kg</p>
      `;
      document.getElementById('results').innerHTML = resultsHTML;
    });
  </script>
</body>
</html>
