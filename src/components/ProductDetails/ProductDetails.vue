<template>
<div class="container">
    <div class="row">
    <!-- For Thumbnail Images -->
    <div class="col-md-1">        
        <div class="row" style="margin-top:5px;" v-for="p in productDetail" :key="p.product_id">
            <a href="#" 
                class="thumbnail" 
                data-toggle="popover" 
                data-full="http://placehold.it/800x800"
                v-for="(image, id) in p.product_img"
                :key="id">
            
                <img 
                    class="img-thumbnail"
                    :src="thumbUrl+image"
                    @mouseover="imgDisp(imgUrl+image)"
                />
            </a>
        </div>
    </div>
    <!-- For Original Image -->
    <div class="col-md-5" style="margin-left:10px; border:1px solid #f0f0f0; height: 405px;" 
        v-for="(p, id) in productDetail" 
        :key="id">
    
        <img 
            :src="thumbImage === '' ? imgUrl+p.product_img[0] : thumbImage"
            class="ori-image"
        />
        
    </div>
    <!-- For Details -->
    <div class="col-md-5 img-detl" 
        v-for="p in productDetail" :key="p.product_id">

        <h2>{{ p.brand_name }}</h2>
        <p style="font-size:18px;">{{ p.product_name }}</p>
        <p style="font-size:28px; font-weight: bolder;">{{ formatPrice(p.product_price) }}</p>
        <p><font style="font-size:20px; font-weight: bolder;">Description: </font>{{ p.product_desc }}</p>

        <button  
            v-if="!show" 
            @click="addCart(p)"
            class="button-cart">
            <i class="fa fa-shopping-cart" aria-hidden="true"></i>&nbsp;Add To Cart
        </button>

        <button  
            v-if="show"
            @click="goCart"
            class="button-cart">
            <i class="fa fa-shopping-cart" aria-hidden="true"></i>&nbsp;Go To Cart
        </button>

        <button 
            class="button-buy">
            <i class="fa fa-shopping-bag" aria-hidden="true"></i>&nbsp;Buy
        </button>
    </div>

    </div>  <!-- End of Row -->      

</div>    
</template>

<script>

export default {
    name: 'Product_Details',

    created () {
        this.routeid = this.$route.params.id;
        this.$store.dispatch('getProductById', this.routeid);
    },

    data () {
        return {
            routeid: '',
            thumbImage: '',
            show: false,
            local: [],
            cart: [],
        }
    },

    computed: {
        productDetail () {
            return this.$store.state.DetailsStore.productDetail;
        },
        imgUrl () {
            return this.$store.state.AppStore.imageUrl;
        },
        thumbUrl () {
            return this.$store.state.AppStore.thumbUrl;
        }      
    },

    mounted () {
        
        this.local = JSON.parse(localStorage.getItem('product_id'));
        if(this.local!=null) {
        
            this.local.map((id) => {
                if( parseInt(id.product_id) === parseInt(this.routeid)) {
                    this.show = true;
                }
            });
        }
    },

    methods: {
        imgDisp (img) {
            this.thumbImage = img;
        },
        formatPrice (value) {
            let pprice = value.toFixed(2).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,")
            return "₹ " + pprice.split('.')[0];
        },
        addCart (product) {
            this.show = true;
            
            this.cart = [];

            this.cart = JSON.parse(localStorage.getItem("product_id"));
            if (this.cart === null) {
                this.cart = [];
            }
            this.cart.push({ product_id: product.product_id });
            localStorage.setItem("product_id", JSON.stringify(this.cart));
            
            if(localStorage.getItem('token') && localStorage.getItem('token')!=null) {
                const obj = {
                    "prod_id" : product.product_id,
                    "prod_name": product.product_name,
                    "prod_price": product.product_price,
                    "prod_img": product.product_img[0],
                    "uid": localStorage.getItem('uid')
                }
                this.$store.dispatch('setCartItems', obj);
            }
            else {
                const obj = {
                    "prod_id" : product.product_id,
                    "prod_name": product.product_name,
                    "prod_price": product.product_price,
                    "prod_img": product.product_img[0],
                }
                this.$store.dispatch('setCartItems', obj);
            }            
            this.$store.dispatch('addToCart');

            this.$notification['success']({
                message: 'Cart',
                description: 'Successfully Added to Cart!!!',
            });
        },
        goCart () {
            this.$router.push({name: 'cart'});            
        }
    }
}
</script>

<style scoped>

body .popover {
    max-width: 830px;
}

.img-thumbnail:hover {
    border: 2px solid #007bff;
}

.container {
    margin-top: 4%;
}
 
.ori-image {
    height: 350px;
    max-width: 100%;
    max-height: 100%;
    transition: opacity .5s linear;
    opacity: 1;
    z-index: 0;
    margin-top: 20px;
}

.img-detl {
    /* font-weight:400;  */
    font-size: 18px;
    text-align: -webkit-left;
}

.button-cart {
    padding: 18px 8px;
    border-radius: 2px;
    box-shadow: 0 1px 2px 0 rgba(0,0,0,.2);
    width: 40%;
    border: none;
    float: left;
    background: #ff9f00;
    color: #fff;
}

.button-buy  {
    padding: 18px 8px;
    border-radius: 2px;
    box-shadow: 0 1px 2px 0 rgba(0,0,0,.2);
    float: right;
    border: none;
    width: 40%;
    background: #fb641b;
    color: #fff;
}

</style>


