<script>
    import { createEventDispatcher } from "svelte";
    import { posts } from "../stores/posts.js";
    import { user } from "../stores/store.js";
    import Fa from "svelte-fa";
    import { faTrashCan } from "@fortawesome/free-solid-svg-icons";
    import { faWrench } from "@fortawesome/free-solid-svg-icons";
    import { faEllipsisVertical } from "@fortawesome/free-solid-svg-icons";
    import { faHeart } from "@fortawesome/free-solid-svg-icons";
    import { faComment } from "@fortawesome/free-solid-svg-icons";
    import { getContext } from "svelte";
    import ImagePopup from "./ImagePopup.svelte";

    const { open } = getContext("simple-modal");
    const showImage = (imageSrc) => open(ImagePopup, { imageSrc: imageSrc });

    export let post;
    let isOwner = false;

    const dispatch = createEventDispatcher();
    let formOpen = false;
    let formDisplay = "none";
    let header = "";
    let body = "";
    let files = null;
    let upvotes = null;
    let imageInput = null;
    let headerErrors = [];
    let bodyErrors = [];
    let upvoteErrors = [];

    const ChangeFormState = () => {
        formOpen = !formOpen;
    };

    const DeletePost = (postId) => {
        const id = postId;
        dispatch("post-deleted", id);
    };
    const UpdatePost = (postId) => {
        const id = postId;
        let errorsAdded = false;
        headerErrors = null;
        bodyErrors = null;
        const formData = new FormData();
        if (header != "") {
            formData.append("header", header);
        }
        if (body != "") {
            formData.append("body", body);
        }
        if (upvotes) {
            formData.append("upvotes", upvotes);
        }
        if (files) {
            formData.append("image", files[0]);
        }

        formData.append("id", id);
        dispatch("post-updated", formData);
    };

    const AddTestComment = (id) => {
        const formData = new FormData();

        formData.append("user_id", $user.id);
        formData.append("post_id", id);
        formData.append("comment_content", "Toto je testovací komentář");

        dispatch("comment-added", formData);
    };

    const UpdateComment = (id, commentId) => {
        const formData = new FormData();

        formData.append("comment_id", commentId);
        formData.append("post_id", id);
        formData.append("comment_content", "Toto je upravený komentář");
        formData.append("upvotes", Math.floor(Math.random() * 100));

        dispatch("comment-updated", formData);
    };

    const DeleteComment = (id, postId) => {
        dispatch("comment-deleted", { id, postId });
    };

    const AddTestResponse = (id, commentId) => {
        const formData = new FormData();

        formData.append("user_id", $user.id);
        formData.append("post_id", id);
        formData.append("comment_id", commentId);
        formData.append("response_content", "Toto je testovací odpověď");

        dispatch("response-added", formData);
    };

    const UpdateResponse = (id, commentId, postId) => {
        const formData = new FormData();

        formData.append("comment_id", commentId);
        formData.append("response_id", id);
        formData.append("post_id", postId);
        formData.append("response_content", "Toto je upravená odpověď");
        formData.append("upvotes", Math.floor(Math.random() * 100));

        dispatch("response-updated", formData);
    };

    const DeleteResponse = (id, postId, commentId) => {
        dispatch("response-deleted", { id, postId, commentId });
    };
</script>

<div class="post">
    <div class="postTop">
        <div class="userData">
            <div
                class="userImage"
                style={"background-image: url(" +
                    post.user.profile_picture +
                    ");"}
            />
            <div class="userInfo">
                <a href={"#/profile/" + post.user.id}>{post.user.name}</a>
                <p>{post.created_at}</p>
            </div>
        </div>
        {#if true}
            <span class="settings">
                <Fa icon={faEllipsisVertical} />
            </span>
        {/if}
    </div>
    <div class="postContent">
        <h1>{post.header}</h1>
        <p>{post.body}</p>
        {#if post.image}
            <div
                class="postImage"
                style={"background-image: url(" + post.image + ");"}
                on:click={showImage(post.image)}
            />
        {/if}
    </div>
    <div class="buttons">
        <button>
            <span>
                {post.upvotes}
                <Fa icon={faHeart} />
            </span>
        </button>
        <button>
            <span on:click={ChangeFormState}>
                <Fa icon={faComment} />
            </span>
        </button>
    </div>
    {#if formOpen}
        <div class="commentForm">
            <p>forma otevřena</p>
        </div>
    {/if}
    <div class="comments">komenty</div>
</div>

<style>
    div.post {
        background-color: var(--nav-bg-color);
        margin-bottom: 1rem;
        padding: 10px;
        border-radius: 20px;
    }

    div.post div.postTop {
        display: flex;
        justify-content: space-between;
    }

    div.post div.postTop span {
        color: var(--white-color);
        transition: 0.1s;
        cursor: pointer;
    }

    div.post div.postTop span:hover {
        color: var(--green-color);
    }

    div.post div.postTop div.userData {
        display: flex;
        align-items: center;
        flex-direction: row;
    }

    div.post div.postTop div.userData div.userImage {
        background-position: center;
        background-size: cover;
        height: 3rem;
        width: 3rem;
        background-color: var(--white-color);
        border-radius: 50%;
    }

    div.post div.postTop div.userData div.userInfo {
        padding-left: 1rem;
    }

    div.post div.postTop div.userData div.userInfo a {
        color: var(--white-color);
        transition: 0.2s;
        text-decoration: none;
    }

    div.post div.postTop div.userData div.userInfo a:hover {
        color: var(--green-color);
    }

    div.post div.postContent {
        padding-bottom: 1rem;
    }

    div.post div.buttons button {
        background-color: transparent;
        border: none;
        transition: 0.1s;
        cursor: pointer;
    }

    div.post div.buttons button span {
        color: var(--white-color);
        font-size: 1.2rem;
        padding: 0 1rem;
    }

    div.post div.buttons button:hover > span {
        color: var(--green-color);
    }

    div.post div.postContent h1 {
        font-family: AlteHaasBold;
        color: var(--white-color);
        padding: 1rem 0;
        word-break: break-word;
    }

    div.post div.postContent p {
        word-break: break-word;
    }

    div.post div.postContent div.postImage {
        width: 100%;
        height: 40rem;
        background-position: center;
        background-size: cover;
        margin: 1rem 0;
    }
</style>