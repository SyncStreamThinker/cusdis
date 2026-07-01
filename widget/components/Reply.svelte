<script>
  import { getContext } from 'svelte'
  import { t } from '../i18n'
  export let parentId

  // form data
  let content = ''
  let nickname = ''

  let loading = false

  export let onSuccess

  const api = getContext('api')
  const setMessage = getContext('setMessage')
  const { appId, pageId, pageUrl, pageTitle } = getContext('attrs')
  const refresh = getContext('refresh')

  async function addComment() {
    if (!content) {
      alert(t('content_is_required'))
      return
    }

    const finalNickname = nickname && nickname.trim() ? nickname.trim() : 'Anonymous'

    try {
      loading = true
      const res = await api.post('/api/open/comments', {
        appId,
        pageId,
        content,
        nickname: finalNickname,
        email: '',
        parentId,
        pageUrl,
        pageTitle,
      })
      await refresh()
      teardown()
      setMessage(t('comment_has_been_sent'))
    } finally {
      loading = false
    }
  }

  function teardown() {
    content = ''
    nickname = ''
    onSuccess && onSuccess()
  }

</script>

<div class="grid grid-cols-1 gap-4">
  <div class="px-1">
    <label class="mb-2 block dark:text-gray-200" for="reply_content">Your comment</label>
    <textarea
      name="reply_content"
      class="w-full p-2 border border-gray-200 h-24 bg-transparent dark:text-gray-100 dark:outline-none"
      title="Your comment"
      bind:value={content}
    />
  </div>

  <div class="px-1">
    <label class="mb-2 block dark:text-gray-200" for="nickname">Name (optional)</label>
    <input
      name="nickname"
      class="w-full p-2 border border-gray-200 bg-transparent dark:text-gray-100 dark:outline-none"
      type="text"
      placeholder="Anonymous"
      title="Name (optional)"
      bind:value={nickname}
    />
  </div>

  <div class="px-1">
    <button
      class="text-sm bg-gray-200 p-2 px-4 font-bold dark:bg-transparent dark:border dark:border-gray-100"
      class:cusdis-disabled={loading}
      on:click={addComment}>{loading ? t('sending') : t('post_comment')}</button
    >
  </div>
</div>
