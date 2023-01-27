<script lang="ts">
	import { ethers } from 'ethers';
	import Erc20ABI from '../abi/erc20.abi.json';

	const erc20Address = '0x09f564d2c4917cff06bf4a06c9Cc659Eb9CecBC2';

	let provider: ethers.providers.Web3Provider;
	let signer: ethers.Signer;
	let address: string;

	let erc20: ethers.Contract;
	let erc20Symbol: string;

	const connect = async () => {
		provider = new ethers.providers.Web3Provider(window.ethereum);
		await provider.send('eth_requestAccounts', []);
		signer = provider.getSigner();
		address = await signer.getAddress();
		erc20 = new ethers.Contract(erc20Address, Erc20ABI, signer);
		erc20Symbol = await erc20.symbol();
	};

	let wei = ethers.utils.parseEther('1').toString();
	let toAddress: string;

	const send = async () => {
		if (!/^[0-9]+$/gi.test(wei)) {
			alert(`${wei} must be numeric`);
			return;
		}
		if (!/^(0x)[a-fA-F0-9]{40,40}/gi.test(toAddress)) {
			alert(`${toAddress} is not an ethereum address`);
			return;
		}

		const tx = await signer.sendTransaction({
			value: ethers.BigNumber.from(wei),
			to: toAddress
		});
		const receipt = await tx.wait();
		alert(`ether successfully sent! hash: ${receipt.transactionHash}`);
	};

	let weiErc20 = ethers.utils.parseEther('1').toString();
	let toAddressErc20: string;

	const sendErc20 = async () => {
		if (!/^[0-9]+$/gi.test(weiErc20)) {
			alert(`${weiErc20} must be numeric`);
			return;
		}
		if (!/^(0x)[a-fA-F0-9]{40,40}/gi.test(toAddressErc20)) {
			alert(`${toAddressErc20} is not an ethereum address`);
			return;
		}

		const tx = await erc20.transfer(toAddressErc20, weiErc20);
		const receipt = await tx.wait();
		alert(`${erc20Symbol} successfully sent! hash: ${receipt.transactionHash}`);
	};
</script>

<button on:click={connect}>connect</button>
<p>{address}</p>

<br />

<h2>send ether</h2>
<p><input type="text" bind:value={wei} size="30" style="text-align: right;" /> wei</p>
<p>{ethers.utils.formatEther(wei)} ether</p>
<p>
	<input type="text" bind:value={toAddress} size="50" placeholder="toAddress" />
	<button on:click={send}>send</button>
</p>

<br />

<h2>send {erc20Symbol}</h2>
<p><input type="text" bind:value={weiErc20} size="30" style="text-align: right;" /> wei</p>
<p>{ethers.utils.formatEther(weiErc20)} {erc20Symbol}</p>
<p>
	<input type="text" bind:value={toAddressErc20} size="50" placeholder="toAddress" />
	<button on:click={sendErc20}>send {erc20Symbol}</button>
</p>
