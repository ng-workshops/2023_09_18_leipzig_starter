# 3 Component interaction - ngOnChanges

## src/app/home/info-box/info-box.component.ts

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChange,
} from '@angular/core';

@Component({
  selector: 'app-info-box',
  standalone: true,
  imports: [CommonModule, MatCardModule],
  templateUrl: './info-box.component.html',
  styleUrls: ['./info-box.component.scss'],
})
export class InfoBoxComponent implements OnChanges {
  private _name!: string;

  @Input()
  message: string | undefined;

  @Input()
  set name(value: string) {
    this._name = value.toLowerCase();
  }

  get name(): string {
    return this._name;
  }

  ngOnChanges(changes: Record<string, SimpleChange>) {
    if (changes['message']) {
      console.log('changes.message', changes['message'].currentValue);
    }

    if (changes['name']) {
      console.log('changes.name', changes['name'].currentValue);
    }

    if (changes['message'] && changes['name']) {
      console.log('Message AND Name changed');
    }
  }
}
```
