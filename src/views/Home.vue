<template>
  <div class="home">
    <h1>Gasless Counter Increase</h1>
    <h2>Value:{{ this.viewvalue }}</h2>
    <input type="number" v-model="value" class="input-box" /><br />

    <button v-on:click="viewValue" class="button">VIEW VALUE</button>
    <button v-on:click="send" class="button">INCREASE VALUE</button>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";
import Web3 from "web3";
import { Biconomy } from "@biconomy/mexa";
export default {
  name: "Home",
  components: {
    HelloWorld,
  },
  data() {
    return {
      viewvalue: null,
      value: 0,
      abiContract: [
        {
          inputs: [
            {
              internalType: "uint256",
              name: "value",
              type: "uint256",
            },
          ],
          name: "setValue",
          outputs: [
            {
              internalType: "uint256",
              name: "",
              type: "uint256",
            },
          ],
          stateMutability: "nonpayable",
          type: "function",
        },
        {
          inputs: [
            {
              internalType: "address",
              name: "_trustedForwarder",
              type: "address",
            },
          ],
          stateMutability: "nonpayable",
          type: "constructor",
        },
        {
          inputs: [],
          name: "counter",
          outputs: [
            {
              internalType: "uint256",
              name: "",
              type: "uint256",
            },
          ],
          stateMutability: "view",
          type: "function",
        },
        {
          inputs: [
            {
              internalType: "address",
              name: "forwarder",
              type: "address",
            },
          ],
          name: "isTrustedForwarder",
          outputs: [
            {
              internalType: "bool",
              name: "",
              type: "bool",
            },
          ],
          stateMutability: "view",
          type: "function",
        },
        {
          inputs: [],
          name: "trustedForwarder",
          outputs: [
            {
              internalType: "address",
              name: "",
              type: "address",
            },
          ],
          stateMutability: "view",
          type: "function",
        },
        {
          inputs: [],
          name: "versionRecipient",
          outputs: [
            {
              internalType: "string",
              name: "",
              type: "string",
            },
          ],
          stateMutability: "view",
          type: "function",
        },
      ],
    };
  },
  methods: {
    send() {
      const biconomy = new Biconomy(window.ethereum, {
        apiKey: "XhhterezE.bb28be9e-a06e-48fd-8986-d62c8223add9",
        debug: true,
      });
      biconomy
        .onEvent(biconomy.READY, () => {
          let web3 = new Web3(biconomy);
          //
          //sending the transactions
          const contractInstance = new web3.eth.Contract(
            this.abiContract,
            "0x9B0dcC366EE9ADa0BA20cD916E75CFA882A62867"
          );
          web3.eth.getAccounts().then((result) => {
            let accountaddress = result[0];
            var tx = {
              from: accountaddress,
              to: "0x9B0dcC366EE9ADa0BA20cD916E75CFA882A62867",
              data: contractInstance.methods.setValue(this.value).encodeABI(),
            };
            web3.eth.sendTransaction(tx).then((result) => {
              console.log(result);
              this.viewValue();
            });
          });
        })
        .onEvent(biconomy.ERROR, (error, message) => {
          alert(error.message);
          alert(error);
        });
    },
    viewValue() {
      const web3 = new Web3(window.ethereum);
      const contractInstance = new web3.eth.Contract(
        this.abiContract,
        "0x9B0dcC366EE9ADa0BA20cD916E75CFA882A62867"
      );
      let getvalue = contractInstance.methods.counter().call();
      getvalue.then((result) => {
        this.viewvalue = result;
      });
    },
    async sign() {
      console.log(this.value);
    },
  },
};
</script>
<style>
.home {
  background-color: #e5eaf5;
  width: 100%;
  bottom: 0;
  position: absolute;
  top: 0;
  left: 0;
}
.input-box {
  width: 60%;
  padding: 20px;
  height: auto;
  font-size: 25px;
  border: 0;
  border-radius: 30px;
  margin: 20px;
}
.button {
  width: 30%;
  height: auto;
  padding: 20px;
  font-size: 20px;
  background-color: #8458b3;
  border: 0;
  border-radius: 20px;
  margin: 30px;
  cursor: pointer;
}
</style>
