<template>
  <div class="hello">
    <h1>{{ msgG }} 於 {{ sec }} 秒後刷新</h1>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="#">V股市</a>
        <!-- The form -->
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <!-- <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                    <a class="nav-link" href="#">我的自選股 <span class="sr-only">(current)</span></a>
                </li>
            </ul> -->
            <form class="form-inline my-4 my-lg-2">
                <input class="form-control mr-sm-2" type="search" placeholder="輸入股票代碼" aria-label="Search" v-model="scode"> 
                <button class="btn btn-outline-success my-2 my-sm-0" @click="getSerach">搜尋</button>
            </form>
        </div>
    </nav>
    <table  class="table">
        <thead>
            <th></th>
            <th>股票代碼</th>
            <th>公司簡稱</th>
            <th>當盤成交價</th>
            <th>當盤成交量</th>
            <th>累積成交量</th>
            <th>開盤價</th>
            <th>最高價</th>
            <th>最低價</th>
            <th>昨收價</th>
        </thead>
        <tbody v-for="(item,index) in stock_data.msgArray">
            <td>
                <button type="button" class="btn btn-primary" data-toggle="button" aria-pressed="false" autocomplete="off" @click="persist(item.c)">
                自選
                </button>
            </td>
            <td>{{ item.c }}</td>
            <td>{{ item.n }}</td>
            <td>{{ item.z }}</td>
            <td>{{ item.tv }}</td>
            <td>{{ item.v }}</td>
            <td>{{ item.o }}</td>
            <td>{{ item.h }}</td>
            <td>{{ item.l }}</td>
            <td>{{ item.y }}</td>
        </tbody>
    </table>
    </div>
</template>
<script>
export default {
  name: 'index',
  data () {
    return {
        msgG : 'STOCK',
        stock_data : [],
        sec:20,
        scode:null,
        
        apitop:`https://mis.twse.com.tw/stock/api/getStockInfo.jsp?ex_ch=`,
        apibottom:`&json=1&delay=0`,
        local_code:'',
    }
   
  },
  mounted () {
        // if (localStorage.scode) {
        //     this.scode = localStorage.getItem('scode')
        // }
        let setAPI = '';
        this.local_code = JSON.parse(localStorage.getItem('scode'));
        this.local_code.forEach(element => {
            setAPI += 'tse_'+element+'.tw|';
        });
        this.axios.get(this.apitop+setAPI+this.apibottom)
            .then((response) => {
                this.stock_data = response.data;
               
                console.log(this.stock_data)
                //console.log(response)
            })
        this.countdown()
        this.getStock()
  },
  methods:{
        getStock (){
            setInterval(() => { 
                let setAPI =''
                if(this.scode != ""){
                    setAPI += `tse_${this.scode}.tw`;
                   
                }else{
                    this.local_code = JSON.parse(localStorage.getItem('scode'));
                    this.local_code.forEach(element => {
                        setAPI += 'tse_'+element+'.tw|';
                    }); 
                }
                
                this.axios.get(this.apitop+setAPI+this.apibottom)
                .then((response) => {
                    this.stock_data = response.data;
                    this.sec = 20
                    console.log(this.stock_data)
                    //console.log(response)
                })
            }, 20000)
        },
        getSerach (){
            let setAPI =''
            if(this.scode != ""){
                    setAPI += `tse_${this.scode}.tw`;
                   
            }else{
                this.local_code = JSON.parse(localStorage.getItem('scode'));
                this.local_code.forEach(element => {
                    setAPI += 'tse_'+element+'.tw|';
                }); 
            }
            this.axios.get(this.apitop+setAPI+this.apibottom)
                .then((response) => {
                    this.stock_data = response.data;
                    console.log(this.stock_data)
                    //console.log(response)
            })
        },
        countdown () {
            if(this.sec!=0)this.sec = this.sec-1
            const that = this
            setTimeout(function () {
                that.countdown()
            }, 1000)
        },
        persist(code) {
            if (localStorage.scode) {
                let oldAry  = JSON.parse(localStorage.getItem('scode'));
                oldAry.push(JSON.parse(code))
                let deduped = oldAry.filter(function (el, i, arr) {
                    return arr.indexOf(el) === i;
                });
                localStorage.setItem('scode',JSON.stringify(deduped))
                console.log(deduped);
               
               
            }else{
                let ary = []
                ary.push(JSON.parse(code))
                localStorage.setItem('scode',JSON.stringify(ary))
            }
           
           
        }
        
  }
}
</script>