<script lang="ts">
    import { onMount, tick } from 'svelte'

    //export to AuditTable
    export let recipients

    //Did not add TS because implied
    let sentToRecipient = []
    let badgeNum = 0
    let element: HTMLDivElement

    //Used Set in case of duplicates and an ID will be needed in the future
    const setRecipients = new Set(recipients)

    //Returns a promise
    const handleResize = async () => {
        let numOfHiddenMess = 0
        sentToRecipient = [...setRecipients].map(recipient => ` ${recipient}`)

        //Looping backwards makings trimming easier
        for (let i = sentToRecipient.length - 1; i > 0; i--) {
            await tick()

            //Checking width of element
            const overflow = element.offsetWidth < element.scrollWidth
            // Change overflow Emails to " ..."
            if (overflow) sentToRecipient[i] = ' ...'
            // `?` is used if index is undefined or null
            if (sentToRecipient[i + 1]?.includes(' ...')) sentToRecipient.pop()
        }

        numOfHiddenMess += recipients.length - sentToRecipient.length
        //From the "for loop", don't want to mutate the array
        if (sentToRecipient[sentToRecipient.length - 1].includes(' ...')) numOfHiddenMess++
        badgeNum = numOfHiddenMess

    }

    onMount(() => {
        handleResize()
    })
    window.addEventListener('resize', handleResize)
</script>



<style lang="scss">
  section {
    //Depending on your style, I would re-style to be consistent with the codebase
  display: flex;
  align-items: center;
  justify-content: space-between;
  }
  // Prefer to use em's (or ems?)
  div.recipient {
    overflow: hidden;
    text-overflow: ellipsis;
    font-size: 16px;
    color: #333333;
    padding: 5px 10px;
  }
  // Prefer to use em's (or ems?)
  div.badgeNum {
    font-size: 16px;
    color: #f0f0f0;
    background-color: $color-primary;
    border-radius: 3px;
    padding: 2px 5px;
  }
</style>

<section>
    <div class="recipient" bind:this={element}>{sentToRecipient}</div>
    {#if  0 < badgeNum}
        <div class="badgeNum">+ {badgeNum}</div>
    {/if}
</section>
