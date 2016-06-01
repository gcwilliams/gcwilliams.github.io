---
layout: post 
title: Angular2, JavaScript libraries and TypeScript 
---

Create an Angular2 project, following the quick start is a good choice [Angular2 QuickStart](https://angular.io/docs/ts/latest/quickstart.html).

I'm going to use bower to install [Lory](https://github.com/meandmax/lory) which is a simple carousel which can easily be used with ES6 imports

    bower install lory --save

Now, following the Lory API we need to create a definition files for TypeScript, first we'll create the defintion of the function used to create a Lory carousel

<!--more-->

{% highlight java %}
export declare function lory(el: any, options?: any) : Lory
{% endhighlight %}

And then we need to defined the return value, which is the API of the Lory carousel.

{% highlight java %}
export declare class Lory {
    
    prev() : void
    
    next() : void
    
    slideTo(index: number) : void
    
    destroy() : void
}
{% endhighlight %}

We'll save this into the `./lory.d.ts` and update `typings.json` with this dependency

{% highlight json %}
{
    "dependencies": {
        "lory": "file:lory.d.ts"
    }
}
{% endhighlight %}

Update the `system.js` config, add the following to `map`

{% highlight javascript %}
var map = {
    'lory': 'bower_components/lory/dist/lory.js'
};
{% endhighlight %}

If using the Angular2 QuickStart, run `npm install`. Now you should be good to use the Lory from Angular2...

{% highlight java %}
@Component({
    selector: "carousel",
    template: `
        <div class="slider js_slider">
            <div class="frame js_frame">
                <ul class="slides js_slides">
                    <li class="js_slide" *ngFor="let photo of photos">
                        <img [src]="photo">
                    </li>
                </ul>
            </div>
        </div>
        <button (click)="prev()">Previous</button>
        <button (click)="next()">Next</button>
    `,
    styles: [
        `.frame {
            width: 500px;
            position: relative;
            font-size: 0;
            line-height: 0;
            overflow: hidden;
            white-space: nowrap;
        }`,
        `.slides {
            display: inline-block;
        }`,
        `.slides li {
            position: relative;
            display: inline-block;
            width: 500px;
        }`,
        `.slides li img {
            width: 100%;
        }`
    ]
})
export class Carousel implements OnChanges {
    
    @Input()
    photos: Array<string>
    
    private lory : Lory
    
    constructor(private el: ElementRef) {}
    
    ngOnChanges(changes) {
        if (changes.photos) {
            if (this.lory) {
                this.lory.destroy()
            }
            setTimeout(() => this.lory = lory(this.el.nativeElement))
        }
    }
    
    next() : void {
        this.lory.next()
    }
    
    prev() : void {
        this.lory.prev()
    }
}
{% endhighlight %}

Find the sample project [here](https://github.com/gcwilliams/angular2-lory)