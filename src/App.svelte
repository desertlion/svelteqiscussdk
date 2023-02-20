<script>
  import QiscusSDK from 'qiscus-sdk-core';

  const qiscus = new QiscusSDK();
  let comments = [];
  let message = '';
  let loggedInUser = '';

  const credentials = [
    {
      unique_id: 'fikri@qiscus.com',
      display_name: 'Fikri (Qiscus)',
      target: 'guest@qiscus.com'
    },
    {
      unique_id: 'guest@qiscus.com',
      display_name: 'Guest',
      target: 'fikri@qiscus.com'
    }
  ];

  const altUser = new URLSearchParams(location.search).get('altUser');
  const user = (altUser) ? credentials[0] : credentials[1];

  const sendMessage = () => {
    qiscus.sendComment(qiscus.selected.id, message)
      .then(res => {
        console.log('berhasil send comment', qiscus.selected);
        document.getElementById('message-form').value = '';
        message = '';
        comments = qiscus.selected.comments;
      });
  }
  qiscus.init({
    AppId: 'sdksample',
    options: {
      loginSuccessCallback(data) {
        loggedInUser = data.user.username;
        qiscus.chatTarget(user.target)
          .then(target => {
            // console.log('berhasil chat target', qiscus);
            comments = target.comments;
          })
      },
      newMessagesCallback(data) {
        console.log('incoming message', data);
        comments = qiscus.selected.comments;
      }
    }
  });
  qiscus.setUser(user.unique_id, 'password', user.display_name);
</script>

<main>
  <p>Welcome, <strong>{ loggedInUser }</strong></p>
  <h3>Isi Comments:</h3>
  <ul>
    {#each comments as comment (comment.id)}
    <li>{ comment.message }</li>
    {/each}
  </ul>
  <hr>
  <textarea name="message"
    id="message-form" cols="30" rows="10"
    on:change={(data) => { message = data.target.value}}></textarea>
  <button on:click={() => { sendMessage(); }}>send</button>
</main>

<style>
</style>
