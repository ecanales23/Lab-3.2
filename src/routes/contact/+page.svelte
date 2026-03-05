<svelte:head>
  <title>Contact me</title>
</svelte:head>
<h1> Contact Me</h1>

    <form action="mailto:canalesemily33@gmail.com" method="get" enctype="text/plain">
        <label>
            Email:
            <input name="email" type="email" value="" >
        </label>
        <label>
            Subject:
            <input name="subject" value="">
        </label>
        <label>
             Message:
            <textarea name="body"></textarea> 
        </label>

        <button>Send</button>
    </form>