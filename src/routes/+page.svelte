<main class="container mx-auto">
  <header class="w-full text-center mb-8">
    <h1 class="text-3xl" >JWT Debugger</h1>
  </header>

  <div class="grid grid-cols-2 w-full gap-8">
    <section>
      <header class="text-center">
	<h2 class="text-2xl">Encoded</h2>
      </header>
      <div>
	<h3 class="text-lg">Raw Token</h3>
      <div
	contenteditable="true"
	class="font-mono h-60 w-full border border-slate-400 resize-none rounded-md drop-shadow"
	bind:textContent={encodedToken}
	bind:innerHTML={encodedTokenHTML}
	on:input={updateTokenFromEncoded}
      />
      </div>
    </section>

    <section>
      <header class="text-center">
	<h2 class="text-2xl">Decoded</h2>
      </header>
      <div class="mb-6">
	<h3 class="text-lg">Header</h3>
	<pre
	  contenteditable="true"
	  bind:textContent={decodedHeader}
	  class="text-emerald-700 min-h-24 font-mono w-full border border-slate-400 resize-none rounded-md drop-shadow"
	  on:input={updateTokenFromDecoded}
	  ></pre>
      </div>


      <div>
	<h3 class="text-lg">Payload</h3>
	<pre
	  contenteditable="true"
	  bind:textContent={decodedPayload}
	  class="text-orange-700 min-h-24 font-mono w-full border border-slate-400 resize-none rounded-md drop-shadow"
	  on:input={updateTokenFromDecoded}
	  ></pre>
      </div>
    </section>
  </div>
</main>


<script lang="ts">
  let encodedToken = ''
  let decodedHeader = ''
  let decodedPayload = ''
  let encodedTokenHTML = ''

  function updateTokenFromDecoded(): void {
    const encodedHeader = btoa(decodedHeader)
    const encodedPayload = btoa(decodedPayload)
    const [_,__,sig] = encodedToken.split('.')
    encodedToken = `${encodedHeader}.${encodedPayload}.${sig}`
    tokenizeToken()

  }

  function formatJsonString(raw: string): string {
    return JSON.stringify(
      JSON.parse(raw),
      null,
      2
    )
  }

  function tokenizeToken() {
    const [header, payload, sig] = encodedToken.split('.')
    const headerElement = `<span class="text-emerald-700">${header}</span>`
    const payloadElement = `<span class="text-orange-700">${payload}</span>`
    const sigElement = `<span class="text-fuchsia-700">${sig}</span>`
    encodedTokenHTML = `${headerElement}.${payloadElement}.${sigElement}`
  }

  function updateTokenFromEncoded(): void {
    tokenizeToken()
  
    const [header, payload, sig] = encodedToken.split('.')
    if (!sig) {
      console.log('not a real jwt')
      return
    }
    decodedHeader = formatJsonString(atob(header))
    decodedPayload = formatJsonString(atob(payload))
  }
</script>
