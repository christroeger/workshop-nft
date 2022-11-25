<script lang='ts'>
    import Mint from './mint.svelte';
    import type {NFT} from '$lib/nft';
    import {getNFTsFromContract} from '$lib/nft';
    import {codingDojoContract, codingDojoContractWithWallet} from '$lib/coding-dojo.store';
    import {onMount} from 'svelte';


    let nfts: NFT[] = [];

    let infoText = '';

    let loading = false;

    onMount(async () => {
        await reload();
    });

    function toggleLoading() {
        loading = !loading;
    }

    async function reload() {
        console.log('Load NFTs');
        nfts = await getNFTsFromContract($codingDojoContract);
    }

    async function transfer(nft: NFT) {
        if (nft.newOwner) {
            toggleLoading();
            console.log(nft);
            infoText = `Transferring to ${nft.newOwner}`;
            await $codingDojoContractWithWallet.transferFrom(nft.owner, nft.newOwner, nft.tokenId)
            infoText = '';
            await reload();
            toggleLoading();
        } else {
            infoText = 'Input not valid';
        }
    }


</script>
<div class='shadow-xl pb-10 mb-20'>
    <h1 class='text-2xl font-bold leading-7 sm:text-3xl sm:truncate text-center	mt-8 text-white'>Welcome to your NFT Collection</h1>
</div>


<div class='container grid grid-cols-3 gap-2 mx-auto'>
    {#each nfts as nft}
        <div class='max-w-sm bg-white rounded-lg shadow-xl my-1'>
            <div class='flex flex-col items-center justify-center'>
                <a>
                    <img class='rounded-t-lg' src={nft.image} alt=''/>
                </a>
            </div>
            <div class='p-5'>
                <a>
                    <h5 class='mb-2 text-2xl font-bold tracking-tight text-gray-700 dark:text-black'>{nft.name}</h5>
                </a>
                <p class='mb-3 font-normal text-gray-900 dark:text-gray-900'>{nft.description}</p>
                <p class='mb-3 font-normal text-gray-900 dark:text-gray-900'>Owner: {nft.owner}</p>
                <div class='mb-3'>
                    <label class='block text-gray-700 text-sm font-bold mb-2' for='newOwner'> Transfer to </label>
                    <input class='shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline'
                           id='newOwner' type='text' placeholder='Text' bind:value={nft.newOwner}>
                </div>
                <div class='flex justify-center mt-2'>
                    {infoText}
                </div>
                <div class='flex justify-center mt-2'>
                    {#if !loading}
                        <button
                                class='w-full px-4 py-2 text-white bg-blue-500 rounded shadow-xl'
                                on:click={transfer(nft)}
                                type='submit'>
                            Transfer
                        </button>
                    {/if}
                </div>
            </div>
        </div>
    {/each}
    <Mint on:nftMinted={reload}></Mint>
</div>


<style>
    :global(body) {
        background-image: -webkit-linear-gradient(0deg, #00B2BB, #9D1681);
    }
</style>
