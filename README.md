<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Recipe Finder</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
  <style>
    .text-shadow {
      text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.4);
    }
  </style>
</head>
<body class="min-h-screen flex items-center justify-center bg-gradient-to-r from-pink-500 to-pink-300">
  <div class="text-center px-4">
    <div class="flex items-center justify-center mb-4">
      <svg class="w-8 h-8 text-white" fill="currentColor" viewBox="0 0 20 20">
        <path fill-rule="evenodd" d="M8 4a6 6 0 100 12A6 6 0 008 4zm8 8a8 8 0 11-1.414-1.414l4.95 
          4.95a1 1 0 01-1.414 1.414l-4.95-4.95A8 8 0 0116 14z" clip-rule="evenodd"/>
      </svg>
      <h1 class="text-white text-4xl font-bold ml-2 text-shadow">Recipe Finder</h1>
    </div>
    <p class="text-white text-lg mb-6">Find delicious recipes by name or ingredient</p>
    <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-xl mx-auto">
      <div class="flex items-center mb-4">
        <svg class="w-5 h-5 text-pink-500 mr-2" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M8 4a6 6 0 100 12A6 6 0 008 4zm8 8a8 8 0 11-1.414-1.414l4.95 
            4.95a1 1 0 01-1.414 1.414l-4.95-4.95A8 8 0 0116 14z" clip-rule="evenodd"/>
        </svg>
        <h2 class="text-lg font-semibold text-gray-800">Search Recipes</h2>
      </div>
      <input id="ingredientInput" type="text" placeholder="Enter recipe name or ingredient..."
        class="w-full border border-pink-300 p-4 rounded-lg text-lg focus:ring-2 focus:ring-pink-400 focus:outline-none" />
      <div class="mt-4 text-right">
        <button id="searchButton" class="bg-pink-500 hover:bg-pink-600 text-white p-2 rounded-lg focus:ring-2 focus:ring-pink-400">
          <svg class="w-5 h-5 inline" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M8 4a6 6 0 100 12A6 6 0 008 4zm8 8a8 8 0 11-1.414-1.414l4.95 
              4.95a1 1 0 01-1.414 1.414l-4.95-4.95A8 8 0 0116 14z" clip-rule="evenodd"/>
          </svg>
        </button>
      </div>
    </div>
  </div>

  <script>
    const input = document.getElementById('ingredientInput');
    const button = document.getElementById('searchButton');

    async function searchRecipes() {
      const query = input.value.trim();
      if (!query) return;

      const previous = document.getElementById('results');
      if (previous) previous.remove();

      const resultsDiv = document.createElement('div');
      resultsDiv.className = "mt-6 text-left text-gray-700";
      resultsDiv.id = "results";

      const container = document.querySelector('.bg-white');
      container.appendChild(resultsDiv);

       
      const nameResponse = await fetch(`https://www.themealdb.com/api/json/v1/1/search.php?s=${query}`);
      const nameData = await nameResponse.json();

      if (nameData.meals) {
        resultsDiv.innerHTML = '<h3 class="text-xl font-semibold mb-4">Recipes Found:</h3>';
        for (const meal of nameData.meals.slice(0, 3)) {
          resultsDiv.innerHTML += `
            <div class="mb-6 p-4 border rounded-lg bg-pink-50 shadow">
              <h4 class="text-lg font-bold mb-2">${meal.strMeal}</h4>
              <img src="${meal.strMealThumb}" alt="${meal.strMeal}" class="w-full h-48 object-cover rounded mb-3">
              <p class="mb-2 text-sm">${meal.strInstructions.substring(0, 200)}...</p>
              <a href="${meal.strSource || `https://www.themealdb.com/meal.php?m=${meal.idMeal}`}" target="_blank"
                class="text-pink-600 hover:underline">View Full Recipe</a>
            </div>`;
        }
        return;
      }

      
      const ingResponse = await fetch(`https://www.themealdb.com/api/json/v1/1/filter.php?i=${query}`);
      const ingData = await ingResponse.json();

      if (ingData.meals) {
        resultsDiv.innerHTML = '<h3 class="text-xl font-semibold mb-4">Recipes with that ingredient:</h3>';
        for (const meal of ingData.meals.slice(0, 5)) {
          resultsDiv.innerHTML += `
            <div class="mb-4 p-4 border rounded-lg bg-pink-50 shadow">
              <h4 class="text-lg font-bold mb-1">${meal.strMeal}</h4>
              <img src="${meal.strMealThumb}" alt="${meal.strMeal}" class="w-full h-48 object-cover rounded mb-2">
              <a href="https://www.themealdb.com/meal.php?m=${meal.idMeal}" target="_blank"
                class="text-pink-600 hover:underline">View Recipe</a>
            </div>`;
        }
      } else {
        resultsDiv.innerHTML = '<p class="text-red-600 font-semibold">No recipes found for this input.</p>';
      }
    }

    button.addEventListener('click', searchRecipes);
    input.addEventListener('keypress', (e) => {
      if (e.key === 'Enter') searchRecipes();
    });
  </script>
</body>
</html>
