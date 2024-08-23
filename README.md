# recommendation-program-project 

#Project Structure
book_recommendation/
│
├── templates/
│   ├── index.html
│   └── recommendations.html
│
├── app.py
└── requirements.txt

#Reccomendation Engine
from flask import Flask, request, render_template
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

app = Flask(__name__)

# Sample data
book_descriptions = [
    "Book about AI and technology.",
    "Novel set in Victorian era.",
    "Guide to Python programming.",
    "Science fiction book with futuristic themes.",
    "Historical drama set in medieval times.",
    "Book on deep learning and neural networks."
]

# Titles corresponding to the descriptions
book_titles = [
    "AI and Technology",
    "Victorian Novel",
    "Python Programming Guide",
    "Futuristic Sci-Fi",
    "Medieval Drama",
    "Deep Learning Guide"
]

# Convert descriptions to vectors
tfidf_vectorizer = TfidfVectorizer(stop_words='english')
tfidf_matrix = tfidf_vectorizer.fit_transform(book_descriptions)

# Calculate cosine similarity
cosine_sim = cosine_similarity(tfidf_matrix, tfidf_matrix)

# Function to get recommendations based on user input
def get_recommendations(title):
    if title not in book_titles:
        return ["No recommendations found."]
    
    # Get the index of the book that matches the title
    idx = book_titles.index(title)
    
    # Get the pairwise similarity scores for all books with that book
    sim_scores = list(enumerate(cosine_sim[idx]))
    
    # Sort the books based on the similarity scores
    sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)
    
    # Get the indices of the most similar books
    book_indices = [i[0] for i in sim_scores]
    
    # Return the titles of the most similar books
    return [book_titles[i] for i in book_indices if i != idx]

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/recommend', methods=['POST'])
def recommend():
    user_input = request.form['user_input']
    recommendations = get_recommendations(user_input)
    return render_template('recommendations.html', recommendations=recommendations)

if __name__ == '__main__':
    app.run(debug=True)


#Yemplate for front End
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Recommendation</title>
</head>
<body>
    <h1>Book Recommendation System</h1>
    <form action="/recommend" method="POST">
        <label for="user_input">Enter a book title you like:</label>
        <input type="text" id="user_input" name="user_input">
        <button type="submit">Get Recommendations</button>
    </form>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Recommendations</title>
</head>
<body>
    <h1>Recommended Books</h1>
    <ul>
        {% for book in recommendations %}
            <li>{{ book }}</li>
        {% endfor %}
    </ul>
    <a href="/">Go Back</a>
</body>
</html>


#Run flask application
Flask
scikit-learn

Install the dependencies
pip install -r requirements.txt

#Run the flask app
python app.py

