<template>
  
  <div class="flex flex-col h-screen justify-between">
      <div id="nav" class="bg-red-600">
        <Nav />
      </div>
      <div class="container w-full mx-auto  flex-grow">
          <div class="w-full px-4 md:px-4 md:mt-8 text-gray-800 leading-normal bg-gray-120 h-full pt-24">
            <router-view/>
          </div>
      </div>
      <Footer />
    </div>
  
</template>

<script>
import Nav from "./components/Nav";
import Footer from "./components/Footer";
export default {
  components: {
    Nav,
    Footer
  }
}
</script>

<style >
</style>
