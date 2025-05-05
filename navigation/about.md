---
layout: page
title: About
permalink: /about/
---

# 📖 My Book Journal

<html lang="en">

<meta charset="UTF-8">
<title>Book Journal</title>

<style>
  body { 
    font-family: Arial, sans-serif; 
    margin: 20px; 
    background-color: #f5f5f5;
  }
  form {
    background-color: #E8C5A4;
    padding: 20px;
    border-radius: 10px;
    width: 300px;
    margin: 0 auto;
  }
  input, textarea, select {
    background-color: #E8C5A4;
    border: 1px solid #ccc;
    width: 100%;
    padding: 8px;
    margin-top: 5px;
    margin-bottom: 15px;
    border-radius: 5px;
  }
  button {
    padding: 10px 15px;
    background-color: #8B0000;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  #libraryDisplay {
    background-color: #8B0000;
    padding: 20px;
    border-radius: 10px;
    color: white;
    margin-top: 20px;
  }
  #libraryDisplay div {
    background-color: #a52a2a;
    border: 1px solid #ccc;
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 8px;
    text-align: center;
  }
  img {
    display: block;
    margin: 0 auto;
    border-radius: 8px;
  }
  .center-buttons {
    text-align: center;
    margin-top: 20px;
  }
</style>

<h1 style="text-align: center;">📖 My Book Journal</h1>

<form id="bookForm">
  <input type="text" id="title" placeholder="Book Title" required><br>
  <input type="text" id="author" placeholder="Author" required><br>
  <input type="text" id="image" placeholder="Image URL" required><br>
  <textarea id="blurb" placeholder="Blurb" required></textarea><br>
  <textarea id="comment" placeholder="Comment" required></textarea><br>
  <select id="likeDislike">
    <option value="like">Like</option>
    <option value="dislike">Dislike</option>
  </select><br>
  <button type="submit">Add Book</button>
</form>

<div class="center-buttons">
  <button onclick="displayLibrary('all')">Show All Books</button>
  <button onclick="displayLibrary('like')">Show Only Liked Books</button>
</div>

<h2 style="text-align: center;">📚 Library</h2>
<div id="libraryDisplay"></div>

<script>
  let library = [];

  if (localStorage.getItem('library')) {
    library = JSON.parse(localStorage.getItem('library'));
    displayLibrary();
  }

  function addBook(title, author, image, blurb, comment, likeDislike) {
    const book = { title, author, image, blurb, comment, likeDislike };
    library.push(book);
    localStorage.setItem('library', JSON.stringify(library));
    displayLibrary();
  }

  function displayLibrary(filter = "all") {
    const libraryDisplay = document.getElementById('libraryDisplay');
    libraryDisplay.innerHTML = '';

    for (let book of library) {
      if (filter === "like" && book.likeDislike !== "like") {
        continue; // Skip books that are not liked
      }

      let bookDiv = document.createElement('div');
      bookDiv.innerHTML = `
        <h2>${book.title}</h2>
        <h4>by ${book.author}</h4>
        <img src="${book.image}" width="120"><br>
        <p><strong>Blurb:</strong> ${book.blurb}</p>
        <p><strong>Comment:</strong> ${book.comment}</p>
        <p>${book.likeDislike === 'like' ? '👍' : '👎'}</p>
      `;
      libraryDisplay.appendChild(bookDiv);
    }
  }

  document.getElementById('bookForm').addEventListener('submit', function(e) {
    e.preventDefault();
    addBook(
      document.getElementById('title').value,
      document.getElementById('author').value,
      document.getElementById('image').value,
      document.getElementById('blurb').value,
      document.getElementById('comment').value,
      document.getElementById('likeDislike').value
    );
    document.getElementById('bookForm').reset();
  });
</script>

</html>
