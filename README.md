<!DOCTYPE html>
<html>
<head>
    <title>Violation Tracker - Student</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            height: 100vh;
        }
        .container {
            width: 300px;
            padding: 20px;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: white;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            margin: 0 10px;
        }
        label, input, button {
            display: block;
            width: 100%;
            margin-bottom: 10px;
        }
        button {
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            margin-top: 20px;
            padding: 10px;
            background-color: #f9f9f9;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        .suggestions {
            border: 1px solid #ccc;
            background-color: #fff;
            max-height: 100px;
            overflow-y: auto;
            border-radius: 5px;
            margin-top: -10px;
            margin-bottom: 10px;
        }
        .suggestion {
            padding: 10px;
            cursor: pointer;
        }
        .suggestion:hover {
            background-color: #f0f0f0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Violation Tracker</h1>
        <form id="searchForm">
            <label for="searchName">Search Name or ID:</label>
            <input type="text" id="searchName" name="searchName" required>
            <div id="suggestionsSearch" class="suggestions"></div>
            <button type="submit">Search</button>
        </form>
        <div id="searchResult" class="result"></div>
    </div>

    <script>
        function getStudents() {
            return JSON.parse(localStorage.getItem('students')) || [];
        }

        function showSuggestions(inputElement, suggestionsDiv, queryCallback) {
            let timeout;
            inputElement.addEventListener('input', function() {
                clearTimeout(timeout);
                timeout = setTimeout(() => {
                    const query = inputElement.value.trim().toLowerCase();
                    suggestionsDiv.innerHTML = '';
                    if (query.length > 0) {
                        const filteredStudents = queryCallback(query);
                        filteredStudents.forEach(student => {
                            const suggestionDiv = document.createElement('div');
                            suggestionDiv.classList.add('suggestion');
                            suggestionDiv.textContent = `${student.name} (${student.id})`;
                            suggestionDiv.addEventListener('click', () => {
                                inputElement.value = student.id;
                                suggestionsDiv.innerHTML = '';
                            });
                            suggestionsDiv.appendChild(suggestionDiv);
                        });
                    }
                }, 300);
            });
        }

        const searchNameInput = document.getElementById('searchName');
        const suggestionsSearchDiv = document.getElementById('suggestionsSearch');

        showSuggestions(searchNameInput, suggestionsSearchDiv, (query) => {
            const students = getStudents();
            return students.filter(student => student.name.toLowerCase().includes(query) || student.id.toLowerCase().includes(query));
        });

        document.getElementById('searchForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const searchName = searchNameInput.value.trim().toLowerCase();
            const students = getStudents();
            const student = students.find(s => s.name.toLowerCase() === searchName || s.id.toLowerCase() === searchName);
            const searchResultDiv = document.getElementById('searchResult');
            if (student) {
                searchResultDiv.innerHTML = `ID: ${student.id}<br>Name: ${student.name}<br>Course: ${student.course}<br>Year: ${student.year}<br>Block: ${student.blockNumber}<br>Violations: ${student.violations.length > 0 ? student.violations.join(', ') : 'None'}`;
            } else {
                searchResultDiv.innerHTML = 'No student found with this name or ID.';
            }
            suggestionsSearchDiv.innerHTML = '';
        });
    </script>
</body>
</html>

