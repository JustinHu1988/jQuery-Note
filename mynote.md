jQuery Note


#Launching Code on Document Ready
To ensure that their code runs after the browser finishes loading the document, many JavaScript programmers wrap their code in a onload function:

    window.onload = function(){
        alert("welcome");
    };

Unfortunately, the code doesn't run until all images are finished downloading, including banner ads. To run code as soon as the document is ready to be manipulated, jQuery has a statement known as the ready event:

    $(document).ready(function(){
        //code
        });