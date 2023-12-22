# Kube-node-red(en)
[![hackmd-github-sync-badge](https://hackmd.io/cocSOGQMR-qzo7DHdwgRsQ/badge)](https://hackmd.io/cocSOGQMR-qzo7DHdwgRsQ)

Kube-node-red is aiming to integrate Kubeflow/Kubebeters with node-red, leveraging node-red's low-code modules, and using Kubeflow resources (e.g. Kubeflow pipeline, Kserve) to enhance its AI/ML ability.
## Table of Contents
<!-- toc -->
- [Architecture](#Architecture)
- [Self-defined Node](#Self-defined-Node)
  * [Prerequisites](#Prerequisites)
  * [snippet.js](#snippet.js)
  * [example.js](#example.js)
  * [example.html](#example.html)
- [Reference](#Reference)

<!-- tocstop -->

# Architecture
![image](https://hackmd.io/_uploads/HJeFeO6Up.png)


# Self-defined Node
## Prerequisites
- `snippet.js`
紀錄以Python編寫之機器學習任務代碼，用以依據前端輸入修改任務流程
- `<example>.js`
節點後端邏輯及前端輸入處理
- `<example>.html`
節點前端邏輯與使用者配置邏輯與使用者配置

## snippet.js

1.將python編寫之機器學習任務用以在kubeflow執行，以js字串儲存於常數供節點調用修改

```javascript=
const EXAMPLE =
`
    data = pd.DataFrame(data, columns=col_names[:])
    data.fillna(value=0, inplace=True)
    data = data.values  
`;
```
2. 將需要依照使用者修改部分以樣板字面值"%s"代換
```javascript=
`   data.fillna(value=%s, inplace=True)
`
```
3. 將該常數輸出
```javascript=
module.exports = {
    
    EXAMPLE,
    
};
```        
##  example.js

1. 導入"util"模組並依照檔案結構導入準備好的snippet.js
```javascript=
const util = require('util');
const snippets = require('../snippets');
```

2. 將使用者於前端之配置替換至目標代碼
```javascript=
example = util.format(snippets.EXAMPLE,config.userinput)
```

3. 將完成修改之代碼以變數儲存於msg物件屬性
```javascript=
node.on('input', function(msg) {
            
            msg.payload += example
            node.send(msg);
    
});
```

##  example.html
1. 依照機器學習任務需求設定相關配置
```javascript=
defaults: {
            userinput: { value: {} }                    
        },
```


2.編寫相應html樣板
```html=
<div class="form-row">
        <label for="node-input-userinput>UserInput</label>
        <input type="text" id="node-input-userinput" />
</div>
```
## Reference
https://github.com/NightLightTw/kube-nodered

https://github.com/kubeflow/pipelines/tree/1.8.21/backend/api/python_http_client

[Kubeflow implementation：add Random Forest algorithm](https://hackmd.io/@ZJ2023/BJYQGMvJ6)

