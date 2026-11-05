## Daily → [[daily/{{date:YYYY}}/{{date:YYYY-MM-DD}}/journal|日記]] · [[daily/{{date:YYYY}}/{{date:YYYY-MM-DD}}/health|健康紀錄]]
- [ ] ! 開機儀式：起床先冥想 10 分鐘
	- [ ] @ brain dump：想法太多就先寫下來
- [ ] ! 主線學習
	- [ ] ~ [[plan/Example Project/Overview|Example Project]] 今日一小步
- [ ] $ 訓練項目 ![[減脂計畫#運動]]
- [ ] $ 側專案
	- [ ] @ [[Side Project]]

## 本日待辦
```tasks
filter by function const r = moment('{{query.file.filenameWithoutExtension}}','YYYY-MM-DD',true); return (task.status.type !== 'DONE' && !!task.due.moment && task.due.moment.isSameOrBefore(r,'day')) || (!!task.done.moment && task.done.moment.isSame(r,'day'));
group by function const d = moment('{{query.file.filenameWithoutExtension}}','YYYY-MM-DD'); return task.status.type === 'DONE' ? '%%2%%' + 'done' : (task.due.moment && task.due.moment.isBefore(d,'day') ? '%%3%%expired' : '%%1%%todo')
sort by due
sort by description
```

## Yesterday Review
![[daily/{{yesterday:YYYY}}/{{yesterday:YYYY-MM-DD}}/journal#review]]

## 身體
![[daily/{{date:YYYY}}/{{date:YYYY-MM-DD}}/health#今日]]

## Reference
- [query](https://publish.obsidian.md/tasks/Queries/About+Queries)
