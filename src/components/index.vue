<template>
  <div class="hello">
    <h1>{{ msgG }} 於 {{ sec }} 後刷新</h1>
    <table  class="table">
        <thead>
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
      
    }
   
  },
  mounted () {
        this.axios.get('https://mis.twse.com.tw/stock/api/getStockInfo.jsp?ex_ch=tse_1101.tw|tse_1102.tw|tse_1103.tw&json=1&delay=0')
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
                this.axios.get('https://mis.twse.com.tw/stock/api/getStockInfo.jsp?ex_ch=tse_1101.tw|tse_1102.tw|tse_1103.tw&json=1&delay=0')
                .then((response) => {
                    this.stock_data = response.data;
                    this.sec = 20
                    console.log(this.stock_data)
                    //console.log(response)
                })
            }, 20000)
        },
        countdown () {
            if(this.sec!=0)this.sec = this.sec-1
            const that = this
            setTimeout(function () {
                that.countdown()
            }, 1000)
        }
        
  }
}
</script>