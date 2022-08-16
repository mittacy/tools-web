<template>
    <div class="app-container">
        <div class="content">
            <Input class="json-input" v-model="jsonString" type="textarea" :autosize="{minRows: 31, maxRows:31}"
                placeholder="Input JSON String..."></Input>
            <div class="json-button">
                <Button type="primary" :loading="!parseComplete" @click="parseJSON">转化>></Button>
            </div>
            <div class="json-shows">
                <json-viewer v-for="jsonObj in jsonObjArr" :key="jsonObj.id" class="json-show" :value="jsonObj.obj"
                    :expand-depth=2 copyable boxed>
                </json-viewer>
            </div>
        </div>
    </div>
</template>

<style lang="less" scoped>
    .app-container {
        .content {
            padding: 20px;
            width: 100%;
            height: 700px;
            display: flex;
            flex-direction: row;

            .json-input {
                width: 40%;
                height: 100%;
            }

            .json-button {
                padding-top: 20px;
                width: 10%;
                height: 100%;
                display: flex;
                justify-content: center;
            }

            .json-shows {
                width: 40%;
                height: 100%;
                overflow: scroll;

                .json-show {
                    margin-bottom: 10px;
                }
            }
        }
    }
</style>

<script>
    export default {
        name: "jsonParse",
        data() {
            return {
                jsonString: '',
                jsonObjArr: [{
                    "id": 1,
                    "obj": {},
                }],
                loopKey: 'context',
                parseComplete: true,
            };
        },
        filters: {},
        methods: {
            parseJSON() {
                if (this.jsonString.trim() === "") {
                    return;
                }
                this.parseComplete = false;

                let arr = this.jsonString.split("\n");

                let tmpJsonObjArr = [];
                let id = 1;
                for (let i = 0; i < arr.length; i++) {
                    if (arr[i].trim() === "") {
                        continue;
                    }

                    let tmp = "";
                    try {
                        tmp = JSON.parse(arr[i]);
                        for (let o in tmp) {
                            if (this.isJSONString(tmp[o])) {
                                tmp[o] = JSON.parse(tmp[o]);
                            }
                        }
                    } catch (e) {
                        this.parseComplete = true;
                        this.$Message.error("输入有误");
                        return;
                    }
                    tmpJsonObjArr.push({
                        "id": id,
                        "obj": tmp
                    });
                    id++;                    
                }

                this.jsonObjArr = tmpJsonObjArr;
                this.parseComplete = true;
            },
            isJSONString(str) {
                try {
                    if (typeof JSON.parse(str) == "object") {
                        return true;
                    }
                } catch (e) {}
                return false;
            }
        },
        created() {},
    }
</script>