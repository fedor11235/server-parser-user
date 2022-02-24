<template>
    <td >
        <input :value="price">
        <input  @click="changePrice($event, price_id)" type="button" value="Сохранить">
    </td>
</template>

<script>
export default {
    name: 'InputPrice',
    props: ['prices', 'price', 'price_id'],

    methods: {
        changePrice(event, id){
        this.prices.forEach(price => {
            if (price.price_id===id){
            price.price=event.path[1].firstChild.value
            }
        })
        alert('save price')
        //there should be a function to send to the server
        }
    },
}
</script>