<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FakeBook - Social Network</title>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@700&family=Open+Sans&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-blue: #1877f2;
            --hover-blue: #166fe5;
            --background-gray: #f0f2f5;
            --text-dark: #1d2129;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Open Sans', sans-serif;
            background-color: var(--background-gray);
        }

        /* Navigation Bar */
        .navbar {
            background: white;
            padding: 8px 15%;
            box-shadow: 0 1px 8px rgba(0,0,0,0.1);
            display: flex;
            align-items: center;
            position: fixed;
            width: 100%;
            top: 0;
            z-index: 1000;
        }

        .logo {
            font-family: 'Montserrat', sans-serif;
            font-size: 2.5rem;
            color: var(--primary-blue);
            display: flex;
            align-items: center;
        }

        .logo img {
            height: 45px;
            margin-right: 12px;
        }

        /* Main Content Grid */
        .main-grid {
            display: grid;
            grid-template-columns: 1fr 2fr 1fr;
            gap: 25px;
            padding: 80px 15% 30px;
            margin-top: 60px;
        }

        /* Post Creation */
        .create-post {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 1px 4px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }

        .post-input {
            width: 100%;
            height: 100px;
            padding: 15px;
            border: 1px solid #dddfe2;
            border-radius: 8px;
            resize: vertical;
            font-size: 1rem;
            margin-bottom: 15px;
        }

        /* News Feed */
        .post {
            background: white;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 1px 4px rgba(0,0,0,0.1);
        }

        .post-actions {
            display: flex;
            gap: 15px;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid #dddfe2;
        }

        .action-button {
            background: none;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: background 0.2s;
        }

        .action-button:hover {
            background: #f0f2f5;
        }

        .liked {
            color: var(--primary-blue) !important;
        }

        /* Sidebars */
        .sidebar {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 1px 4px rgba(0,0,0,0.1);
            height: fit-content;
        }

        .profile-section {
            text-align: center;
        }

        .profile-pic {
            width: 80px;
            height: 80px;
            border-radius: 50%;
            margin-bottom: 15px;
        }
    </style>
</head>
<body>
    <nav class="navbar">
        <div class="logo">
            <img src="image.png" alt="FakeBook Logo">
            FakeBook
        </div>
    </nav>

    <div class="main-grid">
        <!-- Left Sidebar -->
        <div class="sidebar">
            <div class="profile-section">
                <img src="image.png" class="profile-pic" alt="Kino">
                <h2>Kino</h2>
                <p>Pearl Diver</p>
                <p>📍 La Paz, Mexico</p>
                <button class="primary-button" onclick="toggleFollow()" id="followBtn">Follow</button>
                <p id="followerCount">1.2K followers</p>
            </div>
        </div>

        <!-- Main Feed -->
        <div>
            <div class="create-post">
                <textarea class="post-input" placeholder="What's on your mind, Kino?"></textarea>
                <button class="primary-button" onclick="createPost()">Post</button>
            </div>

            <div id="newsFeed"></div>
        </div>

        <!-- Right Sidebar -->
        <div class="sidebar">
            <h3>Trending</h3>
            <div class="trending-list">
                <div class="trend-item">#PearlDiving</div>
                <div class="trend-item">#Coyotito</div>
                <div class="trend-item">#Steinbeck</div>
            </div>
        </div>
    </div>

    <script>
        // App Data
        let fakebookApp = {
            currentUser: {
                name: "Kino",
                followers: 1200,
                isFollowing: false,
                profilePic: "image.png",
                posts: [
                    {
                        id: 1,
                        content: "Just found the most magnificent pearl! Our lives will never be the same 🌊✨ #PearlDiving",
                        likes: 456,
                        comments: [
                            {user: "Juana", text: "Be careful, my love", likes: 12},
                            {user: "Doctor", text: "I can help you with that...", likes: 2}
                        ],
                        shares: 23,
                        isLiked: false,
                        timestamp: "2h ago"
                    },
                    {
                        id: 2,
                        content: "Why won't anyone help Coyotito? The world can be so cruel 😢",
                        likes: 892,
                        comments: [],
                        shares: 45,
                        isLiked: false,
                        timestamp: "5h ago"
                    }
                ]
            },
            trending: [
                {hashtag: "#PearlDiving", posts: "12.3K"},
                {hashtag: "#Coyotito", posts: "8.9K"},
                {hashtag: "#Steinbeck", posts: "5.6K"}
            ]
        };

        // Core Functions
        function toggleFollow() {
            const btn = document.getElementById('followBtn');
            fakebookApp.currentUser.isFollowing = !fakebookApp.currentUser.isFollowing;
            
            btn.textContent = fakebookApp.currentUser.isFollowing ? "Following ✓" : "Follow";
            btn.style.backgroundColor = fakebookApp.currentUser.isFollowing ? "#333" : "var(--primary-blue)";
            
            fakebookApp.currentUser.followers += fakebookApp.currentUser.isFollowing ? 1 : -1;
            updateFollowers();
        }

        function createPost() {
            const postContent = document.querySelector('.post-input').value;
            if(postContent.trim()) {
                const newPost = {
                    id: Date.now(),
                    content: postContent,
                    likes: 0,
                    comments: [],
                    shares: 0,
                    isLiked: false,
                    timestamp: "Just now"
                };
                
                fakebookApp.currentUser.posts.unshift(newPost);
                document.querySelector('.post-input').value = "";
                renderPosts();
            }
        }

        function toggleLike(postId) {
            const post = fakebookApp.currentUser.posts.find(p => p.id === postId);
            post.isLiked = !post.isLiked;
            post.likes += post.isLiked ? 1 : -1;
            renderPosts();
        }

        // UI Updates
        function updateFollowers() {
            document.getElementById('followerCount').textContent = 
                `${fakebookApp.currentUser.followers.toLocaleString()} followers`;
        }

        function renderPosts() {
            const container = document.getElementById('newsFeed');
            container.innerHTML = fakebookApp.currentUser.posts.map(post => `
                <div class="post">
                    <p>${post.content}</p>
                    <div class="post-meta">
                        <span class="timestamp">${post.timestamp}</span>
                        <div class="post-actions">
                            <button class="action-button ${post.isLiked ? 'liked' : ''}" 
                                onclick="toggleLike(${post.id})">
                                👍 Like (${post.likes.toLocaleString()})
                            </button>
                            <button class="action-button">
                                💬 Comment (${post.comments.length})
                            </button>
                            <button class="action-button">
                                ↗️ Share (${post.shares})
                            </button>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        // Initialization
        function initApp() {
            renderPosts();
            updateFollowers();
        }

        // Start the app
        window.onload = initApp;
    </script>
</body>
</html>
