--- 
layout: post
title: Sprint 5 Reflection Blog
description: Mock PPR for Sprint 5
permalink: /sprint5-blog/
---

# Purpose
1) The purpose of our group's program is to provide a platform where readers can foster their love of books through multiple different ways. As a general overview, we have pages where readers can add their favorite books to their profile, suggest their favorite books to other people, get random book recommendations based on their favorite genres, discuss top books with other readers, and add books to their wishlist for future reads, and add books to their cart for purchase options. Through these features, we ensure that users have a platform to endorse all of their favorite reading endeavors

2) My individual feature was a book reviews page. This is similar to a Goodreads page where users can comment on their favorite books and add their review and general thoughts about the book for other users to see and know.

## Input/Output Requests
1) API request and response: The request being sent is the comment and when the comment is successfully added, the server responds with a success message.
<img src="{{site.baseurl}}/images/r&r.png" alt="API Request/Response">
<br>
<br>

2) Postman requests
Postman Get Request for Comments
<img src="{{site.baseurl}}/images/get.png" alt="Postman Get">

Post Requests to Add A Comment: 
<img src="{{site.baseurl}}/images/post.png" alt=Post>

Put Request to Update Comment: 
<img src="{{site.baseurl}}/images/put.png" alt=Put>


Delete Request to Delete Comment:
<img src="{{site.baseurl}}/images/delete.png" alt=Delete>

3) **Restoring data:**
- Run db_backup.py first to back up the data to user_management.bak
- Then run db_init.py to initialize the database
- Then run db_restore.py to restore data to whatever is stored in the user_management.bak

## List Requests
1) **Discuss formatting response data (JSON) from API into DOM:** The ```fetchComments()``` function makes a GET request to ```/api/comments?book_id=${currentBook.id}```, which returns an array of comments in JSON format. Then, the comments are formatted into the DOM by the ```displayComments()``` function, which creates a new <div> for each comment and appends it to the ```commentsList```.

**Get comments code:**
```
fetch(`${pythonURI}/api/comments?book_id=${currentBook.id}`)
   .then(response => response.json())
   .then(data => {
     if (data.comments) {
       displayComments(data.comments);  // Display the comments
     }
   })
```

**Display Comments code:**
```
function displayComments(comments) {
  const commentsList = document.getElementById('commentsList');
  commentsList.innerHTML = ''; // Clear previous comments
  comments.forEach(comment => {
    const commentDiv = document.createElement('div');
    commentDiv.classList.add('comment-box');
    commentDiv.innerHTML = `
      <div class="comment-text">
        <strong>User ${comment.user_id}</strong><br>${comment.comment_text}
      </div>
    `;
    commentsList.appendChild(commentDiv); // Add the comment to the list
  });
}
```

2) **Discuss queries from database where you extract a Python List (rows). Mention how these queries are provide by a 3rd party library:** ```Comments.query.filter_by(book_id=book_id).all()``` filters comments by ```book_id``` and returns all matching rows as a list of ```Comments``` objects. In the next line, the code iterates over the ```comments_query``` (which is a Python list) and return a dictionary representation of each comment.
The result is a Python list of dictionaries, which represents each comment's data in a more JSON friendly format.

Code:
```
def get_comments_for_book(book_id=None):
    if book_id:
        comments_query = Comments.query.filter_by(book_id=book_id).all()  # Fetches all comments for a specific book
    else:
        comments_query = Comments.query.all()  # Fetches all comments
    
    return [{
        "id": comment.id,
        "book_id": comment.book_id,
        "user_id": comment.user_id,
        "comment_text": comment.comment_text
    } for comment in comments_query]  # Returns comments in a dictionary
```

3) **Discuss methods in "class"  you created to work with columns (create, read, update, delete)**: 
 **Create:**
 I created a new instance of the ```Comments``` class, populated it with data (attributes that correspond to columns in the database), and then add it to the session to be committed.
 ```
 def create(self):
        # Check if the comment already exists for this user and book
        existing_comment = Comments.query.filter_by(
            book_id=self.book_id,
            user_id=self.user_id,
            comment_text=self.comment_text
        ).first()

        if existing_comment:
            # If the comment already exists, return a message indicating no change
            return {"message": "Comment already exists for this book and user."}, 400

        try:
            db.session.add(self)
            db.session.commit()
            return {"message": "Comment added successfully."}, 201
        except Exception as e:
            db.session.rollback()
            raise e
```

**Read:**
I performed queries using SQLAlchemy's querying methods to retrieve records from the ```Comments``` table.
```
def read(self):
        return {
            'id': self.id,
            'book_id': self.book_id,
            'user_id': self.user_id,
            'comment_text': self.comment_text
        }
```

**Update**:
I fetch an existing comment, modify its attributes (which are the columns in the table), and then commit the changes to the database.
```
    def update(self, inputs):
        if not isinstance(inputs, dict):
            return self

        book_id = inputs.get("book_id", None)
        user_id = inputs.get("user_id", None)
        comment_text = inputs.get("comment_text", "")

        if book_id:
            self.book_id = book_id
        if user_id:
            self.user_id = user_id
        if comment_text:
            self.comment_text = comment_text

        try:
            db.session.commit()
        except IntegrityError as e:
            db.session.rollback()
            print(f"IntegrityError occurred: {e}")
            return None

        return self
```

**Delete:**
I fetch a comment, delete it from the session, and commit the change to remove it from the database.
```
def delete(self):
        try:
            db.session.delete(self)
            db.session.commit()
        except Exception as e:
            db.session.rollback()
            raise e
```


## Algorithmic code request. Show the definition of code blocks to handle a request.
1) Discuss API class (code block) you used to perform  get, post, put, and delete methods.
GET: Retrieves all comments for a specific book, identified by book_id, and returns them in a JSON response, or returns an error if no comments are found.

POST: Adds a new comment for a specific book and user by validating the input data and then saving it to the database, or returns an error if any required fields are missing.

PUT: Updates the text of an existing comment, identified by comment_id, and commits the changes to the database, or returns an error if the comment is not found or the text is missing.

DELETE: Deletes a specific comment, identified by comment_id, from the database and returns a success message, or returns an error if the comment is not found.
```
Comments Route (GET, POST, PUT, DELETE):
@bookreview_api.route('/comments', methods=['GET', 'POST'])
def manage_comments():
    if request.method == 'GET':
        book_id = request.args.get('book_id')

        if not book_id:
            return jsonify({'error': 'Book ID is required'}), 400

        comments = get_comments_for_book(book_id)
        if comments:
            return jsonify({'comments': comments})
        else:
            return jsonify({'message': 'No comments found for this book'}), 404

    elif request.method == 'POST':
        try:
            data = request.get_json()

            book_id = data.get('book_id')
            user_id = data.get('user_id')
            comment_text = data.get('comment_text')

            if not book_id or not user_id or not comment_text:
                return jsonify({'error': 'Missing required fields: book_id, user_id, or comment_text'}), 400

            book = Book.query.get(book_id)
            if not book:
                return jsonify({'error': 'Book not found'}), 404

            user = User.query.get(user_id)
            if not user:
                return jsonify({'error': 'User not found'}), 404

            new_comment = Comments(
                book_id=book_id,
                user_id=user.id,
                comment_text=comment_text
            )

            db.session.add(new_comment)
            db.session.commit()

            return jsonify({
                'id': new_comment.id,
                'book_id': new_comment.book_id,
                'user_id': new_comment.user_id,
                'comment_text': new_comment.comment_text
            }), 201

        except Exception as e:
            print(f"Error while adding comment: {e}")
            db.session.rollback()
            return jsonify({'error': 'Internal Server Error'}), 500

    PUT and DELETE for Comments (Route: /api/comments/<comment_id>):
    @bookreview_api.route('/comments/<int:comment_id>', methods=['PUT', 'DELETE'])
    def update_delete_comment(comment_id):
        comment = Comments.query.get(comment_id)

        if not comment:
            return jsonify({'error': 'Comment not found'}), 404

        if request.method == 'PUT':
            try:
                data = request.get_json()
                comment_text = data.get('comment_text')

                if not comment_text:
                    return jsonify({'error': 'Comment text is required'}), 400

                comment.comment_text = comment_text
                db.session.commit()

                return jsonify({
                    'id': comment.id,
                    'book_id': comment.book_id,
                    'user_id': comment.user_id,
                    'comment_text': comment.comment_text
                })

            except Exception as e:
                print(f"Error while updating comment: {e}")
                db.session.rollback()
                return jsonify({'error': 'Internal Server Error'}), 500

        elif request.method == 'DELETE':
            try:
                db.session.delete(comment)
                db.session.commit()
                return jsonify({'message': 'Comment deleted successfully'}), 200
            except Exception as e:
                print(f"Error while deleting comment: {e}")
                db.session.rollback()
                return jsonify({'error': 'Internal Server Error'}), 500
```

2) Discuss a method/procedure in class that contains sequencing, selection, and iteration.<br>
    1) **Sequencing**: Each function follows a structured sequence<br>
        1) Recieves reqeuest to add or recieve comment data<br>
        2) Validates request<br>
        3) Query the database to find comments associated with the book<br>
        4) Return a response with comment data<br><br>
    2) **Conditionals**: <br>
        1) GET: must have a valid ```book_id``` to get comments for that book<br>
        2) POST: must have valid ```user_id```,```book_id```, and some ```comment_text```<br>
        3) PUT: must have a proper ```comment_id```<br>
        4) DELETE: must have a proper ```comment_id```<br><br>
    3) **Iteration**: used in the get method for comments. For comments, it extracts comment data for each comment associated with a specific book<br>
        Comments: 
        ```
        return [{
            "id": comment.id,
            "book_id": comment.book_id,
            "user_id": comment.user_id,
            "comment_text": comment.comment_text
        } for comment in comments_query]
        ```

       


3) **Discuss the parameters (body of request) and return type (jasonify) of the function.**

The server is handling a request to retrieve comments, the parameters are book id, user id, and comment text. The function returns a JSON response, using jsonify, which contains either the success message with the comment data or an error message with a relevant status code.
```
# Comments Route (GET, POST, PUT, DELETE)
@bookreview_api.route('/comments', methods=['GET', 'POST'])
def manage_comments():
    if request.method == 'GET':
        book_id = request.args.get('book_id')

        if not book_id:
            return jsonify({'error': 'Book ID is required'}), 400

        comments = get_comments_for_book(book_id)
        if comments:
            return jsonify({'comments': comments})
        else:
            return jsonify({'message': 'No comments found for this book'}), 404
```

## Call to Algorithm request.  Show the definition of code block to make a request.
1) **Discuss the call/request to the method with Algorithm (fetch to endpoint).**
The server is making a request to obtain data from the endpoint. In the above code, the server makes a request to retrieve from the /api/comments endpoint, which contains all the comments in the backend.

```
@bookreview_api.route('/comments', methods=['GET', 'POST'])
def manage_comments():
    if request.method == 'GET':
        book_id = request.args.get('book_id')

        if not book_id:
            return jsonify({'error': 'Book ID is required'}), 400

        comments = get_comments_for_book(book_id)
        if comments:
            return jsonify({'comments': comments})
        else:
            return jsonify({'message': 'No comments found for this book'}), 404
```


2) **Discuss the return/response from the method with Algorithm (fetch) and how you handle data.** 
The backend responds by returning all the comments associated with the book id in the parameters. Any comments with the book id in the parameters are returned, along with their book id and the user id that it was posted with. 

3) **Show how changing data or method triggers a different response, specifically normal conditions and error conditions.**

Adding an invalid book id will return an error message. Changing the book id to a valid id will change the comments associated with that book. If no comments are found for that book, an error message will appear stating 'No comments found for this book'

<img src="{{site.baseurl}}/images/error.png" alt=Error>



## Frontend code: 

```
  function addComment() {
    const commentInput = document.getElementById('commentInput');
    const commentText = commentInput.value.trim();
    const userId = document.getElementById('userIdInput').value.trim();

    if (commentText === '') {
      alert('Comment cannot be empty.');
      return;
    }

    if (!userId) {
      alert('Please enter a valid User ID.');
      return;
    }

    const commentData = {
      book_id: currentBook.id,
      user_id: userId,
      comment_text: commentText
    };

    // Debug: Log the comment data to verify it's correct
    console.log('Sending comment data:', commentData);

    fetch(`${pythonURI}/api/comments`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(commentData)
    })
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok');
      }
      return response.json();
    })
    .then(data => {
      console.log('Response data:', data);
      if (data.success) {
        // Optionally append the new comment immediately without waiting for fetchComments()
        const commentList = document.getElementById('commentsList');
        
        const newComment = document.createElement('div');
        newComment.classList.add('comment');
        newComment.innerHTML = `
          <p><strong>User ${userId}:</strong> ${commentText}</p>
        `;
        commentList.appendChild(newComment); // Add the new comment to the list

        // Clear the input field
        commentInput.value = '';
      } else {
        alert('Successfully added comment! Refresh to check');
      }
    })
    .catch(error => {
      console.error('Error adding comment:', error);
      alert('Failed to add comment.');
    });
  }

  // Fetch comments from the backend
  function fetchComments() {
    fetch(`${pythonURI}/api/comments?book_id=${currentBook.id}`)
      .then(response => response.json())
      .then(data => {
        if (data.comments) {
          displayComments(data.comments);
        } else {
          alert('No comments found for this book.');
        }
      })
      .catch(error => {
        console.error('Error fetching comments:', error);
        alert('Failed to fetch comments.');
      });
  }
```

## Discuss a method/procedure in class that contains sequencing, selection, and iteration.
  1) **Sequencing**: Each function follows a structured sequence

    1) Recieves reqeuest to add or recieve comment data

    2) Validates request

    3) Query the database to find comments associated with the book

    4) Return a response with comment data


    2) **Conditionals**: 

        1) GET: must have a valid ```book_id``` to get comments for that book

        2) POST: must have valid ```user_id```,```book_id```, and some ```comment_text```

        3) PUT: must have a proper ```comment_id```

        4) DELETE: must have a proper ```comment_id```

    3) **Iteration**: used in the get method for comments. For comments, it extracts comment data for each comment associated with a specific book
    
        Comments: 
        ```
        return [{
            "id": comment.id,
            "book_id": comment.book_id,
            "user_id": comment.user_id,
            "comment_text": comment.comment_text
        } for comment in comments_query]
        ```