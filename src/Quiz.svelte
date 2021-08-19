<script>
import { onMount } from 'svelte';
import localforage from 'localforage';



let idquiz = "iniadalahidquiz-1"

let indexpertanyaan = 0;

let pertanyaan = [
    {
        pertanyaan:"Apa bahasa pemrograman yang memiliki fitur arrow function?",
        jawaban:[
           {
                id:0,
                content:"Javascript"
           },
           {
                id:1,
                content:"Python"
           },
           {
               id:2,
               content:"Golang"
           }
        ]
    },
    {
        pertanyaan:"Siapa tokoh utama dari anime Tokyo Revenger?",
        jawaban:[
           {
                id:0,
                content:"Paijo"
           },
           {
                id:1,
                content:"Sukimin"
           },
           {
               id:2,
               content:"Takemichi"
           }
        ]
    },
    {
        pertanyaan:"Siapa orang paling ganteng di dunia?",
        jawaban:[
           {
                id:0,
                content:"Yudha"
           },
           {
                id:1,
                content:"Yudha"
           },
           {
               id:2,
               content:"Yudha"
           }
        ]
    },
]

let jawaban = new Array(pertanyaan.length);

let jawabanIsComplete = false;

let checkIsJawabanComplete = ()=>{
    if(jawaban.filter((jwb)=>jwb!==undefined).length===pertanyaan.length){
        jawabanIsComplete = true;
    }
}

let saveJawaban = ()=>{
    localforage.setItem(idquiz, jawaban);
    checkIsJawabanComplete();
}

onMount(()=>{
    localforage.getItem(idquiz,(err,value)=>{
        if(value!==null){
            jawaban=value;
            checkIsJawabanComplete();
        }
    });
    
})


</script>

<main>

    <div style="position:absolute;left:50px;color:white;fontWeight:bold;display:grid;grid-template-columns: 1fr 1fr 1fr;">
       {#each jawaban as jwb, index} 
            <div 
            on:click={()=>{
                indexpertanyaan = index;
            }}
            style={`padding:10px;cursor:pointer;border-bottom:${(indexpertanyaan===index && !jawabanIsComplete) ? "solid 2px red":""};background-color:${(jwb) ? 'orange':'whitesmoke'};color:${(jwb) ? 'white':'black'}`}>{index+1}</div>
       {/each}
    </div>

    <div style={`display:${jawabanIsComplete ? "none":""}`}>
        {#if !jawabanIsComplete && indexpertanyaan>=pertanyaan.length-1}
                <button
                on:click={()=>{
                    indexpertanyaan=indexpertanyaan-1;
                }}
                >Sebelumnya</button>
        {:else if indexpertanyaan===0}
            <button on:click={()=>{
                saveJawaban();
                indexpertanyaan=indexpertanyaan+1;
            }}>Selanjutnya</button>
        {:else if indexpertanyaan===pertanyaan.length-1}
            <button
            on:click={()=>{
                saveJawaban();
                indexpertanyaan=indexpertanyaan-1;
            }}
            >Sebelumnya</button>
        {:else}
            <button
            on:click={()=>{
                saveJawaban();
                indexpertanyaan=indexpertanyaan-1;
            }}
            >Sebelumnya</button>
            <button
            on:click={()=>{
                saveJawaban();
                indexpertanyaan=indexpertanyaan+1;
            }}
            >Selanjutnya</button>
        {/if}
    </div>
	<h1>Aplikasi Quiz Sederhana</h1>
	{#if jawabanIsComplete}
        <div>
            Quiz Selesai, ini adalah jawaban kamu :
            <br><br>
           {
                JSON.stringify(jawaban)
            }
        </div>
    {:else}
        <div>
            <label>Pertanyaan Ke {indexpertanyaan+1} :</label>
            <div style="margin-top:20px;fontWeight:bold;font-size:30px">{pertanyaan[indexpertanyaan].pertanyaan}</div>
            <div style="margin-top:40px;display:flex;flex-direction:column">
            {#each pertanyaan[indexpertanyaan].jawaban as jwb,index}
                <button
                style={`background-color:${jawaban[indexpertanyaan] && jawaban[indexpertanyaan].indexjawaban===index ? "orange":""}`}
                on:click={()=>{
                    // alert(JSON.stringify({
                    //     indexpertanyaan:indexpertanyaan,
                    //     jawaban:jwb.content,
                    //     indexjawaban:index
                    // }));
                    jawaban[indexpertanyaan] = {
                        indexpertanyaan:indexpertanyaan,
                        jawaban:jwb.content,
                        indexjawaban:index
                    }
                    saveJawaban();
                    if(indexpertanyaan<jawaban.length-1){
                        indexpertanyaan=indexpertanyaan+1;
                    }
                    
                }}
                >{jwb.content}</button>
            {/each}
            </div>
        </div>
    {/if}
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>