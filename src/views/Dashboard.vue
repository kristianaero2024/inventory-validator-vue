<template>
    <div class="main-container">
        <select v-model="selectSite" class="select-site">
            <option selected value="">-- Select Site-- </option>
            <option v-for="item in sites" :value="item.name">
                {{ item.name }}
            </option>
        </select>
        <br />
        <div class="export-btn-container">
            <button :class="choosedPlatform === 'shopify' ? 'choosed' : ''" color="primary"
                class="export-btn export-shopify" @click="exportShopify"> SHOPIFY</button>
            <button :class="choosedPlatform === 'shopee' ? 'choosed' : ''" color="primary"
                class="export-btn export-shopee" @click="exportShopee"> SHOPEE</button>
            <button :class="choosedPlatform === 'lazada' ? 'choosed' : ''" color="primary"
                class="export-btn export-lazada" @click="exportLazada"> LAZADA</button>
            <button :class="choosedPlatform === 'zalora' ? 'choosed' : ''" color="primary"
                class="export-btn export zalora" @click="exportZalora"> ZALORA</button>
        </div>
        <br />
        <div class="upload">
            <label>Omisell Inventory: </label>
            <input type="file" class="upload-omisell" @change="handleOmisellUpload" />
            <!--<button @click="uploadOmisellCSV">Upload Omisell CSV</button>-->
        </div>
        <br />
        <div class="upload-platform" v-if="showUploadPlatform">
            <label>Upload Platform Inventory: </label>
            <input type="file" class="upload-omisell" @change="handleMPUpload" />
            <!--<button @click="uploadOmisellCSV">Upload Omisell CSV</button>-->
        </div>
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

        <div class="process-text" v-html="processText"></div>


    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            choosedPlatform: null,
            showUploadPlatform: false,
            file: null,
            shopifyReadyToDL: false,
            generatedGID: null,
            processText: "Choose Process",
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
                }
            ]
        }
    },

    methods: {

        async handleOmisellUpload(event) {
            console.log('omisell upload')

            const formData = new FormData()

            const files = Array.from(event.target.files);

            this.file = files[0]

            console.log(this.file)

            formData.append('file', this.file)

            try {
                console.log("test2")
                const response = await axios.post(this.apiURL + '/upload-omisell-csv?platform=' + this.choosedPlatform + '&site=' + this.selectSite, formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                    },
                }).then(response => {
                    console.log(response)
                })
            } catch (error) {
                console.error(error)
            }
        },

        async handleMPUpload(event) {
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
        },

        async generateReport() {
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

                        this.processText = "Report Generated <br />"
                        this.processText += "Per SKU : <a href='" + this.apiURL + "/exports/comparisonResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a><br />"
                        this.processText += "Per Brand : <a href='" + this.apiURL + "/exports/groupedResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a>"
                    })
                } catch (error) {
                    console.error(error)
                }
            }

            if (this.choosedPlatform == 'shopify') {


                if (!this.file) {
                    alert('Please select a file')
                    return
                }

                const formData = new FormData()

                formData.append('file', this.file)

                try {
                    this.processText = "Pulling inventory from Shopify, Please wait. . ."

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

                                this.processText = this.processText + ' .'

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

                                                    this.processText = "Report Generated <br />"
                                                    this.processText += "Per SKU : <a href='" + this.apiURL + "/exports/comparisonResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a><br />"
                                                    this.processText += "Per Brand : <a href='" + this.apiURL + "/exports/groupedResults-" + this.choosedPlatform + '-' + this.selectSite + ".csv'>Download here</a>"
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
    margin-bottom: 20px;
    margin-top: 50px;
    color: #000;
}

.select-site {
    width: 200px;
    border: 1px solid #ccc;
    padding: 5px;
    margin-left: 11px;
    border-radius: 6px;
    margin-bottom: 21px;
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