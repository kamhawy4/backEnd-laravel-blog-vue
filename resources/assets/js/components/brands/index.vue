<template>
<div  v-loading="isLoading" >

<div class="row">
   <div class="col-md-12">
    <!-- BEGIN EXAMPLE TABLE PORTLET-->
      <div class="portlet light bordered">
         <div class="portlet-title">
             <div class="caption font-dark">
                 <i class="fa fa-users font-dark"></i>
                 <span class="caption-subject bold   ">Table of Brands</span>
             </div>  
         </div>

         <flash-message  class="myCustomClass"></flash-message>
        <div class="portlet-body">
    <div class="table-toolbar">
        <div class="row">
            <div class="col-md-6">
                <router-link to="brands/create" class="btn btn-primary">Add New Brands</router-link>
            </div>
        </div>
    </div>

    <table style="margin-top: 20px"  class="table table-striped table-bordered table-hover table-checkable order-column" >
        <thead>
            <tr>
                <th>id</th>
                <th>name</th>
                <th>image</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <tr  v-for="(brand,index) in brands" >
                <td>{{brand.id}}</td>
                <td>{{brand.name}}</td>
                <td><img  style="width: 43px;height: 32px;" v-bind:src="'/uploads/photo/'+brand.image"></td>
                <td>
                   <router-link  class="btn btn-primary" :to="{name:'edibrands' , params:{brands_id:brand.id }}" > <i class="fa fa-pencil"></i> Edit</router-link>
                    <a class="btn btn-danger" href="javascript:;"  @click="DeleteBrands(brand.id,index)" >Delete</a>
                </td>
            </tr>
        </tbody>
    </table>

    <div  class="pagination">
    	<button  class="btn btn-default" @click="fetchPaginate(pagination.prev_page_url)" 
    	:disabled="!pagination.prev_page_url" >Previos</button>
    	<span> page {{pagination.current_page}} of {{pagination.last_page}}</span>
    	<button  class="btn btn-default" @click="fetchPaginate(pagination.next_page_url)" 
    	:disabled="!pagination.next_page_url" >Next</button>
    	
    </div>
</div>
</div>
   <!-- END EXAMPLE TABLE PORTLET-->
    </div>
</div>
</div>
</template>

<script>
export default {
    data(){
    	return {
         brands:[],
         isLoading:false,
         url:'/api/dashboard/brands',
         pagination:[],
    	}
      },mounted(){
          this.getBrands();
      },methods:{
      	getBrands(){
      	  let $this = this;
      	  this.isLoading = true;
		  this.$http.get(this.url).then(response => {
		  this.isLoading = false;
		  this.brands = response.body.data;      
		  $this.makepaginate(response.body);
		  }, response => {
       this.isLoading = false;
			 this.flash('Something went wrong', 'error',{timeout:3000});
		  });
		},
		DeleteBrands:function(id,index) {
        this.$swal({
                  title: 'Are you sure?',
                  text: "You won't be able to revert this!",
                  type: 'warning',
                  showCancelButton: true,
                  confirmButtonColor: '#3085d6',
                  cancelButtonColor: '#d33',
                  confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                  if (result.value) {
                			  this.isLoading = true;
                			  this.$http.delete('/api/dashboard/brands/'+id).then(response => {
                			  this.isLoading = false;
                		      this.brands.splice(index,1);
                          this.$swal(
                            'Deleted!',
                            'Your file has been deleted.',
                            'success'
                          )
                        this.flash('Data has been successfully Delete', 'success' ,{timeout:3000});
                      }, response => {
                        this.isLoading = false;
                        this.flash('Something went wrong', 'error',{timeout:3000});
                      });
                  }
                }) 
                
		},makepaginate:function(data) {
			let pagination = {
			   current_page : data.current_page,
			   last_page    :  data.last_page,
			   next_page_url : data.next_page_url,
			   prev_page_url : data.prev_page_url,
			}
			this.pagination = pagination
		},
		fetchPaginate:function(url) {
			this.url = url
			this.getBrands()
		}
      }
    }
</script>
