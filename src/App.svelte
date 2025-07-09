<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import Detail from "./routes/Detail.svelte"
  import QuestionCreate from "./routes/QuestionCreate.svelte"
  import Navigation from "./components/Navigation.svelte"
  import UserCreate from "./routes/UserCreate.svelte"
  import UserLogin from "./routes/UserLogin.svelte"
  import QuestionModify from "./routes/QuestionModify.svelte"
  import AnswerModify from "./routes/AnswerModify.svelte"
  import Profile from './routes/Profile.svelte'; // Profile 컴포넌트 import

  const routes = {
    '/': Home,
    '/detail/:question_id': Detail,
    '/question-create': QuestionCreate,
    '/user-create': UserCreate,
    '/user-login': UserLogin,
    '/question-modify/:question_id': QuestionModify,
    '/answer-modify/:answer_id': AnswerModify,
    '/profile': Profile,
  }
</script>

<Navigation /><main class="container">
<Router {routes}/>
</main>
<style>
  .container{
    padding-top: 1rem;
  }
</style>