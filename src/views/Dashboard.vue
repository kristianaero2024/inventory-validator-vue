<template>
    <div class="main-container">
        <div class="process-text" v-html="processText"></div>
        <div class="error-msg" v-html="errorMsg"></div>
        <label>Choose Site: </label><br />
        <select v-model="selectSite" class="select-field select-site">
            <option selected value="">-- select -- </option>
            <option v-for="item in sites" :value="item.name">
                {{ item.name }}
            </option>
        </select>
        <br />
        <div class="export-btn-container">
            <label>Choose Platform: </label><br />
            <select v-model="choosedPlatform" class="select-field">
                <option value="">-- select --</option>
                <option value="shopify">Shopify</option>
                <option value="lazada">Lazada</option>
                <option value="shopee">Shopee</option>
                <option value="zalora">Zalora</option>
            </select>
            <!--<button :class="choosedPlatform === 'shopify' ? 'choosed' : ''" color="primary"
                class="export-btn export-shopify" @click="exportShopify"> SHOPIFY</button>
            <button :class="choosedPlatform === 'shopee' ? 'choosed' : ''" color="primary"
                class="export-btn export-shopee" @click="exportShopee"> SHOPEE</button>
            <button :class="choosedPlatform === 'lazada' ? 'choosed' : ''" color="primary"
                class="export-btn export-lazada" @click="exportLazada"> LAZADA</button>
            <button :class="choosedPlatform === 'zalora' ? 'choosed' : ''" color="primary"
                class="export-btn export zalora" @click="exportZalora"> ZALORA</button>
            -->
        </div>
        <div class="upload">
            <label>Omisell Inventory: </label><br />
            <input type="file" class="upload-omisell" @change="handleOmisellUpload" />
            <!--<button @click="uploadOmisellCSV">Upload Omisell CSV</button>-->
        </div>
        <div class="upload-platform" v-if="showUploadPlatform">
            <label>Upload Platform Inventory: </label>
            <input type="file" class="upload-omisell" @change="handleMPUpload" />
            <!--<button @click="uploadOmisellCSV">Upload Omisell CSV</button>-->
        </div>
        <br />
        <br />
        <button v-show="choosedPlatform != null" class="submit-generate-report" @click="generateReport">GENERATE
            REPORT</button>
        <!--  
        <br />
        <div class="upload">
            <label>Shopee Inventory: </label>
            <input type="file" />
        </div>
        <br />
        <div class="upload">
            <label>Lazada Inventory: </label>
            <input type="file" />
        </div>
        <br />
        <div class="upload">
            <label>Zalora Inventory: </label>
            <input type="file" />
        </div>
        -->
        <br />

        
        <div class="loader" v-if="isLoading"></div>

    </div>
</template>

<script>
import axios from 'axios';
import { errorMessages } from 'vue/compiler-sfc';

export default {
    data() {
        return {
            errorMsg: '',
            isLoading: false,
            choosedPlatform: '',
            showUploadPlatform: false,
            file: null,
            shopifyReadyToDL: false,
            generatedGID: null,
            processText: "",
            //apiURL: 'http://localhost:3000',
            apiURL: 'https://inventory-validator.onrender.com',
            selectSite: '',
            sites: [
                {
                    'name': "RESTOERUN"
                },
                {
                    'name': "TTC"
                },
                {
                    'name': "GRIND"
                },
                {
                    'name': "ROX"
                },
                {
                    'name': "BRATPACK"
                },
                {
                    'name': "JANSPORT"
                },
                {
                    'name': "HYDROFLASK"
                },
                {
                    'name': "EYS"
                },
                {
                    'name': "AETREX"
                },
            ]
        }
    },

    methods: {

        async handleOmisellUpload(event) {
            this.isLoading = true
            const formData = new FormData()
            const files = Array.from(event.target.files);
            this.file = files[0]

            formData.append('file', this.file)

            try {
                console.log("test2")
                const response = await axios.post(this.apiURL + '/upload-omisell-csv?platform=' + this.choosedPlatform + '&site=' + this.selectSite, formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                    },
                }).then(response => {
                    console.log(response)
                    this.isLoading = false
                })
            } catch (error) {
                console.error(error)
            }
        },

        async handleMPUpload(event) {
            this.isLoading = true
            console.log('omisell upload')

            const formData = new FormData()

            const files = Array.from(event.target.files);

            this.file = files[0]

            console.log(this.file)

            formData.append('file', this.file)

            try {
                console.log("test2")
                const response = await axios.post(this.apiURL + '/upload-mp-csv?type=' + this.choosedPlatform + '&site=' + this.selectSite, formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                    },
                }).then(response => {
                    console.log(response)
                    this.isLoading = false
                })
            } catch (error) {
                console.error(error)
            }
        },

        /*async uploadOmisellCSV(){
            console.log('uploadOmisellCSV')

            if(!this.file){
                alert('Please select a file')
                return
            }

            const formData = new FormData()

            formData.append('file', this.file)

            try{
                console.log("test2")
                const response = await axios.post(this.apiURL + '/upload-omisell-csv', formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                    },
                })
            } catch (error) {
                console.error(error)
            }
        },*/

        /*
        exportShopify() {
            this.choosedPlatform = 'shopify'
            this.showUploadPlatform = false
            console.log("test123: " + this.choosedPlatform)
        },

        exportShopee() {
            this.choosedPlatform = 'shopee'
            this.showUploadPlatform = true
            console.log("test123: " + this.choosedPlatform)
        },

        exportLazada() {
            this.choosedPlatform = 'lazada'
            this.showUploadPlatform = true
            console.log("test123: " + this.choosedPlatform)
        },

        exportZalora() {
            this.choosedPlatform = 'zalora'
            this.showUploadPlatform = true
            console.log("test123: " + this.choosedPlatform)
        },*/

        async generateReport() {
            this.isLoading = true
            const apiURL = this.apiURL
            const selectSite = this.selectSite

            if (this.choosedPlatform == 'lazada' || this.choosedPlatform == 'shopee' || this.choosedPlatform == 'zalora') {
                if (!this.file) {
                    alert('Please select a file')
                    return
                }

                const formData = new FormData()

                formData.append('file', this.file)

                try {
                    this.processText = "Reading " + this.choosedPlatform + " inventory of " + this.selectSite + ". . ."

                    console.log("test2")
                    const params = {
                        type: this.choosedPlatform,
                        site: this.selectSite
                    }

                    const paramsGenerateReport = {
                        //platformCSV: response.data.fileName,
                        type: this.choosedPlatform,
                        site: this.selectSite
                    }

                    const responseGenerateReport = await axios.post(apiURL + '/compare-inventory-mp', paramsGenerateReport, {
                        headers: {
                            'Content-Type': 'application/json'
                        }
                    })
                    .then(response => {
                        console.log(response)
                        console.log('response.error_msg: ' + response.error_msg)

                        if(response.error_msg){
                            this.errorMsg = response.error_msg
                        }else{
                            this.processText = "Report Generated <br />"
                            this.processText += "Per SKU : <a href='" + this.apiURL + "/exports/comparisonResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a><br />"
                            this.processText += "Per Brand : <a href='" + this.apiURL + "/exports/groupedResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a>"
                        }

                        this.isLoading = true
                    })
                } catch (error) {
                    console.error(error)
                }
            }

            if (this.choosedPlatform == 'shopify') {
                this.isLoading = true

                if (!this.file) {
                    alert('Please select a file')
                    return
                }

                const formData = new FormData()

                formData.append('file', this.file)

                try {
                    this.processText = "Pulling inventory from Shopify, Please wait . . ."

                    console.log("test2")
                    const params = {
                        type: this.choosedPlatform,
                        site: this.selectSite
                    }

                    const response = await axios.post(apiURL + '/export', params, {
                        headers: {
                            'Content-Type': 'application/json'
                        }
                    })

                    if (response.data) {
                        const checkString = response.data.substring(0, 13);

                        console.log('checkString: ' + checkString)

                        if (checkString == 'gid://shopify') {
                            this.generatedGID = response.data

                            console.log("GID: " + response.data)

                            const generateBulkInterval = setInterval(async () => {
                                const paramsBulk = {
                                    gid: response.data,
                                    site: selectSite
                                }

                                //console.log(apiURL, paramsBulk)

                                const responseBulk = await axios.post(apiURL + '/export-bulk', paramsBulk, {
                                    headers: {
                                        'Content-Type': 'application/json'
                                    }
                                })

                                console.log("responseBulk: " + responseBulk.data)

                                //this.processText = this.processText + ' .'

                                if (responseBulk.data != null) {
                                    clearInterval(generateBulkInterval)

                                    this.processText = "Analyzing data . . ."
                                    console.log("generated")

                                    const paramsConvertToCSV = {
                                        jsonlFile: responseBulk.data,
                                        type: this.choosedPlatform,
                                        site: this.selectSite
                                    }

                                    await axios.post(apiURL + '/convert-jsonl-to-csv', paramsConvertToCSV, {
                                        headers: {
                                            'Content-Type': 'application/json'
                                        }
                                    })
                                        .then(async response => {

                                            console.log('convert-jsonl-to-csv: ' + response);

                                            const paramsGenerateReport = {
                                                platformCSV: response.data.fileName,
                                                type: this.choosedPlatform,
                                                site: this.selectSite
                                            }

                                            const responseGenerateReport = await axios.post(apiURL + '/compare-inventory', paramsGenerateReport, {

                                                headers: {
                                                    'Content-Type': 'application/json'
                                                }
                                            })
                                                .then(response => {
                                                    console.log(response)
                                                    console.log('response.error_msg: ' + response.data.error_msg)

                                                    if(response.data.error_msg){
                                                        this.errorMsg = response.data.error_msg
                                                        this.processText = null
                                                    }else{
                                                        this.processText = "Report Generated <br />"
                                                        this.processText += "Per SKU : <a href='" + this.apiURL + "/exports/comparisonResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a><br />"
                                                        this.processText += "Per Brand : <a href='" + this.apiURL + "/exports/groupedResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a>"
                                                    }
                                                    this.isLoading = false
                                                })
                                        })
                                }

                            }, 5000)
                        }
                    }
                } catch (error) {
                    console.error(error)
                }
            }

            if (this.choosedPlatform == 'shopee') {
            }

            if (this.choosedPlatform == 'lazada') {
            }

            if (this.choosedPlatform == 'zalora') {
            }

        }
    }
}
</script>

<style>
body,
body a {
    color: #000;
}

/* HTML: <div class="loader"></div> */
.loader {
  width: 60px;
  aspect-ratio: 1;
  display: grid;
  grid: 50%/50%;
  animation: l4-0 2s infinite steps(1);
  position: fixed;
  left: 0;
  right: 0;
  top: 30%;
  margin: 0 auto;
}
.loader::before {
  content: "";
  transform-origin: bottom; 
  animation: 
    l4-1 0.5s infinite linear alternate,
    l4-2 0.5s infinite steps(1) alternate;
}
@keyframes l4-0 {
  0%  {transform:scale(1 , 1) rotate(0deg)}
  25% {transform:scale(1 ,-1) rotate(90deg)}
  50% {transform:scale(-1,-1) rotate(0deg)}
  75% {transform:scale(-1, 1) rotate(90deg)}
}
@keyframes l4-1 {
  0%   {transform:perspective(150px) rotateX(  0deg)}
  100% {transform:perspective(150px) rotateX(180deg)}
}
@keyframes l4-2 {
  0% {background:#ffa516}
  50%{background:#f03355}
}

.submit-generate-report {
    background-color: red;
    padding: 10px;
    border-radius: 5px;
    color: #fff;
}

label {
    color: #000;
    font-weight: bold;
}

.error-msg {
    text-align: center;
    margin-bottom: 40px;
    font-weight: bold;
    color: crimson;
}

.upload-omisell {
    color: #000;
}

.main-container {
    background-color: #fff;
}

.choosed {
    border: 3px solid #000;
}

.upload label {
    font-weight: bold;
}

.process-text {
    font-weight: bold;
    margin-bottom: 40px;
    /* margin-top: 50px; */
    color: #000;
    text-align: center;
}
.select-field{
    width: 200px;
    border: 1px solid #ccc;
    padding: 5px;
    border-radius: 6px;
    margin-bottom: 21px;
}

.select-site {
    
}

button.export-btn.export-shopify {
    background-color: green;
}

button.export-btn.export-shopee {
    background-color: coral;
    color: #fff;
}

button.export-btn.export-lazada {
    background-color: cornflowerblue;
    color: #fff;
}

button.export-btn.export.zalora {
    background-color: #000;
    color: #fff;
}

.export-btn {
    float: left;
    padding: 9px;
    margin: 10px;
    border-radius: 5px;
    font-size: 13px;
    font-weight: bold;
    color: #fff;
}

.main-container {
    border: 1px solid #ccc;
    border-radius: 10px;
    padding: 20px;
    margin-top: -200px;
    padding-bottom: 50px;
    position: ABSOLUTE;
    left: 0;
    right: 0;
    width: 700px;
    margin: 0 auto;
    top: 50px;
}

.export-btn-container {
    overflow: auto;
}
</style>