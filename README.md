# okopk
Step 1: Set Up a New Laravel Project
Install Laravel (if not already installed):

bash
Copy code
composer create-project --prefer-dist laravel/laravel laravel_crud
cd laravel_crud
Run the development server:

bash
Copy code
php artisan serve
Visit http://127.0.0.1:8000 in your browser.

Step 2: Create a Database
Create a database using tools like phpMyAdmin or MySQL CLI.
Update the .env file with your database credentials:
env
Copy code
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=your_password
Step 3: Create a Model, Migration, and Controller
Run this command to create all three:

bash
Copy code
php artisan make:model Post -mcr
This creates:
Post model in app/Models/Post.php
A migration file in database/migrations
A controller PostController in app/Http/Controllers/PostController.php
Step 4: Define the Database Schema
In the migration file (e.g., 2024_12_08_000000_create_posts_table.php), define your table structure:

php
Copy code
public function up()
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->text('content');
        $table->timestamps();
    });
}
Run the migration to create the table:

bash
Copy code
php artisan migrate
Step 5: Define Routes
In routes/web.php, add these routes:

php
Copy code
use App\Http\Controllers\PostController;

Route::resource('posts', PostController::class);
Step 6: Implement CRUD Methods
In PostController, add the logic for each method:

Display All Posts (index method):

php
Copy code
public function index()
{
    $posts = Post::all();
    return view('posts.index', compact('posts'));
}
Show Create Form (create method):

php
Copy code
public function create()
{
    return view('posts.create');
}
Store New Post (store method):

php
Copy code
public function store(Request $request)
{
    Post::create($request->only(['title', 'content']));
    return redirect()->route('posts.index');
}
Show Edit Form (edit method):

php
Copy code
public function edit(Post $post)
{
    return view('posts.edit', compact('post'));
}
Update Post (update method):

php
Copy code
public function update(Request $request, Post $post)
{
    $post->update($request->only(['title', 'content']));
    return redirect()->route('posts.index');
}
Delete Post (destroy method):

php
Copy code
public function destroy(Post $post)
{
    $post->delete();
    return redirect()->route('posts.index');
}
Step 7: Create Views
Inside the resources/views/posts folder, create these files:

Index View (index.blade.php):

blade
Copy code
<a href="{{ route('posts.create') }}">Create New Post</a>
<ul>
    @foreach($posts as $post)
        <li>
            {{ $post->title }}
            <a href="{{ route('posts.edit', $post->id) }}">Edit</a>
            <form action="{{ route('posts.destroy', $post->id) }}" method="POST" style="display:inline;">
                @csrf
                @method('DELETE')
                <button type="submit">Delete</button>
            </form>
        </li>
    @endforeach
</ul>
Create Form (create.blade.php):

blade
Copy code
<form action="{{ route('posts.store') }}" method="POST">
    @csrf
    <input type="text" name="title" placeholder="Title">
    <textarea name="content" placeholder="Content"></textarea>
    <button type="submit">Save</button>
</form>
Edit Form (edit.blade.php):

blade
Copy code
<form action="{{ route('posts.update', $post->id) }}" method="POST">
    @csrf
    @method('PUT')
    <input type="text" name="title" value="{{ $post->title }}">
    <textarea name="content">{{ $post->content }}</textarea>
    <button type="submit">Update</button>
</form>
