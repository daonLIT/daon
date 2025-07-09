<script>
    import { onMount } from 'svelte';
    import { push } from 'svelte-spa-router';
    import { is_login, username } from '../lib/store.js';
    import fastapi from '../lib/api';

    let userProfile = null;
    let isLoading = true;
    let error = null;

    // 현재 URL 경로를 가져오기 위한 라우터의 location
    import { location } from 'svelte-spa-router';
    let currentPath;
    location.subscribe(path => {
        currentPath = path;
    });

    onMount(() => {
        if (!$is_login) {
            alert("로그인이 필요합니다.");
            push('/user-login');
            return;
        }
        
        fastapi('get', '/api/user/profile', {}, 
            (json) => {
                userProfile = json;
                isLoading = false;
            },
            (err) => {
                error = err;
                isLoading = false;
                alert("프로필 정보를 가져오는 데 실패했습니다.");
                console.error("Error fetching user profile:", err);
            }
        );
    });

    function navigateToQuestionDetail(questionId) {
        push(`/detail/${questionId}`);
    }

    function navigateToAnswerQuestionDetail(answerQuestionId) {
        push(`/detail/${answerQuestionId}`);
    }
</script>

<div class="container my-3">
    <div class="row">
        <div class="col-md-8 mx-auto">
            <h1 class="mb-4">{$username}님의 프로필</h1>

            {#if isLoading}
                <p>프로필 정보를 로드 중입니다...</p>
            {:else if error}
                <p class="text-danger">오류: {error.detail || "프로필 정보를 가져올 수 없습니다."}</p>
            {:else if userProfile}
                <div class="card mb-4">
                    <div class="card-body">
                        <h5 class="card-title">기본 정보</h5>
                        <p class="card-text"><strong>사용자 이름:</strong> {userProfile.username}</p>
                        <p class="card-text"><strong>이메일:</strong> {userProfile.email}</p>
                    </div>
                </div>

                <div class="card mb-4">
                    <div class="card-body">
                        <h5 class="card-title">작성한 질문</h5>
                        {#if userProfile.questions.length > 0}
                            <ul class="list-group list-group-flush">
                                {#each userProfile.questions as question}
                                    <li class="list-group-item">
                                        <a href="/detail/{question.id}" on:click|preventDefault={() => navigateToQuestionDetail(question.id)}>
                                            {question.subject}
                                        </a>
                                        <small class="text-muted float-end">
                                            {new Date(question.create_date).toLocaleString()}
                                        </small>
                                    </li>
                                {/each}
                            </ul>
                        {:else}
                            <p class="card-text">작성한 질문이 없습니다.</p>
                        {/if}
                    </div>
                </div>

                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">작성한 답변</h5>
                        {#if userProfile.answers.length > 0}
                            <ul class="list-group list-group-flush">
                                {#each userProfile.answers as answer}
                                    <li class="list-group-item">
                                        <a href="/detail/{answer.question_id}" on:click|preventDefault={() => navigateToAnswerQuestionDetail(answer.question_id)}>
                                            {answer.content.substring(0, 50)}{#if answer.content.length > 50}...{/if} (질문 ID: {answer.question_id})
                                        </a>
                                        <small class="text-muted float-end">
                                            {new Date(answer.create_date).toLocaleString()}
                                        </small>
                                    </li>
                                {/each}
                            </ul>
                        {:else}
                            <p class="card-text">작성한 답변이 없습니다.</p>
                        {/if}
                    </div>
                </div>
            {:else}
                <p>프로필 정보를 불러올 수 없습니다.</p>
            {/if}
        </div>
    </div>
</div>