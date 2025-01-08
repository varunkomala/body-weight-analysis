<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Comprehensive Body Weight Analysis</title>
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
      
      const name = document.getElementById('name').value;
      const age = parseInt(document.getElementById('age').value);
      const gender = document.getElementById('gender').value;
      const height = parseFloat(document.getElementById('height').value) / 100;  // convert to meters
      const weight = parseFloat(document.getElementById('weight').value);

      // Validate the input data
      if (isNaN(age) || isNaN(height) || isNaN(weight) || age <= 0 || height <= 0 || weight <= 0 || !name) {
        alert("Please enter valid inputs.");
        return;
      }

      // BMI Calculation
      const bmi = weight / (height * height);
      let bmiCategory = '';
      if (bmi < 18.5) bmiCategory = 'Underweight';
      else if (bmi >= 18.5 && bmi < 24.9) bmiCategory = 'Normal';
      else if (bmi >= 25 && bmi < 29.9) bmiCategory = 'Overweight';
      else bmiCategory = 'Obese';

      // BMR Calculation using Harris-Benedict equation
      const bmr = gender === 'male' ? 
                  10 * weight + 6.25 * (height * 100) - 5 * age + 5 : 
                  10 * weight + 6.25 * (height * 100) - 5 * age - 161;

      // Body Fat Percentage Estimate
      const bodyFatPercentage = gender === 'male' ?
                                1.20 * bmi + 0.23 * age - 16.2 :
                                1.20 * bmi + 0.23 * age - 5.4;

      // Visceral Fat Estimate (Approximated based on body fat percentage)
      const visceralFat = (bodyFatPercentage / 3).toFixed(1);

      // Subcutaneous Fat Estimate (Approximated as a fraction of body fat)
      const subcutaneousFat = (bodyFatPercentage * 0.7).toFixed(1);

      // Skeletal Muscle Estimate (Approximation based on weight and gender)
      const skeletalMuscle = gender === 'male' ? 
                             (50 + (weight * 0.33)).toFixed(1) : 
                             (45 + (weight * 0.28)).toFixed(1);

      // Ideal Weight Calculation (Devine Formula)
      const idealWeightMin = (50 + 2.3 * ((height * 100 / 2.54) - 60)).toFixed(1);
      const idealWeightMax = (idealWeightMin * 1.1).toFixed(1);

      // Body Age Estimate (simplified formula, relative to BMI)
      const bodyAge = age + (bmi - 22) * 0.5;

      // Display Results with User's Name
      const resultsHTML = `
        <h2>Results for ${name}</h2>
        <p><strong>BMI:</strong> ${bmi.toFixed(2)} (${bmiCategory})</p>
        <p><strong>BMR:</strong> ${bmr.toFixed(2)} kcal/day</p>
        <p><strong>Body Fat Percentage:</strong> ${bodyFatPercentage.toFixed(2)}%</p>
        <p><strong>Visceral Fat Estimate:</strong> ${visceralFat}%</p>
        <p><strong>Subcutaneous Fat Estimate:</strong> ${subcutaneousFat}%</p>
        <p><strong>Skeletal Muscle Estimate:</strong> ${skeletalMuscle} kg</p>
        <p><strong>Body Age:</strong> ${Math.round(bodyAge)} years</p>
        <p><strong>Ideal Weight Range:</strong> ${idealWeightMin} kg to ${idealWeightMax} kg</p>
      `;
      document.getElementById('results').innerHTML = resultsHTML;
    });
  </script>
</body>
</html>
